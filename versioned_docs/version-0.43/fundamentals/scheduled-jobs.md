---
sidebar_label: Scheduled Jobs
title: Scheduled Jobs
---

# Scheduled Jobs

Many enterprise applications need certain actions to run in the background on a set schedule - every night, every week, or every few minutes. Shesha calls these **Scheduled Jobs**, and runs them on top of [Hangfire](https://www.hangfire.io/). Typical uses include:

- Sending bulk notifications, such as reminders
- Cleaning up or updating data after a certain timeframe (for example, expired shopping carts, data synchronisation)
- Monitoring, such as checking system availability or watching for a file to appear

---

## Design Principles

To keep a Scheduled Job robust, follow these principles:

- **Make your job fault-tolerant.** Implement your job logic as an [idempotent operation](https://stackoverflow.com/questions/1077412/what-is-an-idempotent-operation), so it can be run multiple times without leaving the system in an inconsistent state. If a job fails or is interrupted (for example by a hardware or network failure), an administrator should be able to simply rerun it without risking data corruption or duplicate operations.
- **Commit changes regularly and atomically.** Any data changes your job makes should be committed to the database [atomically](<https://en.wikipedia.org/wiki/Atomicity_(database_systems)>) and at regular intervals. This matters most for long-running jobs, so an interrupted job doesn't have to restart from scratch. Only commit at logical points where the system stays consistent.
- **Log progress regularly, but not too often.** Use the `Log` property to report progress, especially for long-running jobs. Logging adds overhead, so batch your log calls (for example, every 100 records) rather than logging on every iteration.
- **Think about performance.** Scheduled jobs often process large volumes of data, and NHibernate's overhead can become significant at scale. For bulk operations, consider [using ADO.NET directly](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/retrieving-and-modifying-data) instead of going through NHibernate.

---

## Implementing a Scheduled Job

To implement a Scheduled Job, create a class that inherits from `ScheduledJobBase` and override `DoExecuteAsync`.

:::note No need to wrap in try/catch
`ExecuteAsync` (the method Hangfire actually calls) already wraps your `DoExecuteAsync` logic and catches unhandled exceptions, so you don't need a top-level try/catch in your job.
:::

The job's schedule is set via the `cronString` parameter of the `[ScheduledJob]` attribute. To help build a valid cron string, use a tool such as [cronmaker.com](http://www.cronmaker.com).

### Example: A Bulk Notification Job

**Example - Send appointment reminders every night at 2am:**

```cs
/// <summary>
/// Sends reminders of appointments the day before the appointments.
/// </summary>
[ScheduledJob("9bc54591-55fb-4e2e-91b6-199cb9c187d0",
    startupMode: StartUpMode.Automatic,
    cronString: "0 2 * * *",  // Executes everyday at 2am
    description: "Sends reminders of appointments the day before the appointments.")]
public class SendAppointmentReminderNotificationJob : ScheduledJobBase<ScheduledJobStatistic>, ITransientDependency
{
    // Sql to retrieve the list of valid Appointments scheduled for tomorrow for which a Reminder notification has not yet been sent according to the audit log in AbpTenantNotifications
    private const string SQL_SELECT_REMINDERS_TO_SEND = @"SELECT Id FROM Health_Appointments WHERE
                                            IsDeleted = 0
                                        AND StatusLkp = 3 /*Booked*/
                                        AND [AppointmentTime] >= @fromDate AND [AppointmentTime] < @toDate
                                        AND Id NOT IN (SELECT EntityId FROM AbpTenantNotifications WHERE NotificationName = @templateId)
                                ";

    /// <summary>
    /// Implements the logic to be executed on the specified schedule.
    /// </summary>
    public override async Task DoExecuteAsync(CancellationToken cancellationToken)
    {
        var bookingNotificationSender = IocManager.Resolve<IBookingNotificationSender>();
        var appointmentsRepo = IocManager.Resolve<IRepository<CdmAppointment, Guid>>();

        Log.Info("Started...");

        var numProcessed = 0;
        using (var session = IocManager.Resolve<ISessionFactory>().OpenSession())
        {
            // Retrieving list of notifications to be sent as an ADO.NET DataReader for improved performance, since volumes may be significant
            var reader = GetReaderForAllAppointmentsStillToNotify(session, DateTime.Now.Date.AddDays(1), DateTime.Now.Date.AddDays(2), NotificationTemplateIds.AppointmentReminder);

            while (reader.Read())
            {
                var appointmentId = reader.GetGuid(0);

                try
                {
                    var appointment = appointmentsRepo.Get(appointmentId);
                    await bookingNotificationSender.NotifyAppointmentReminderAsync(appointment);
                    JobStatistics.NumSucceeded++;
                }
                catch (Exception ex)
                {
                    Log.Error($"Failed to send message for appointment Id:{appointmentId}", ex);
                    JobStatistics.NumErrors++;
                }
                finally
                {
                    numProcessed++;
                    if (numProcessed % 100 == 0)    // Only logging every 100 messages to reduce overhead
                        Log.Info($"{numProcessed} appointments have been processed.");
                }
            }
        }

        Log.Info($"All appointments have been processed - Sent: {JobStatistics.NumSucceeded} | Failed: {JobStatistics.NumErrors} | Skipped: {JobStatistics.NumSkipped}");
    }
}
```

:::warning `JobStatistics` requires the generic base class
`JobStatistics` is not available on the plain `ScheduledJobBase` class - it's only defined on `ScheduledJobBase<TStat>` (or `ScheduledJobBase<TStat, TParams>`). If you don't need to track statistics, inherit from `ScheduledJobBase` directly and skip `JobStatistics` entirely. If you do, inherit from `ScheduledJobBase<ScheduledJobStatistic>` as shown above, or from your own statistics class as shown below.
:::

---

## Custom Job Statistics

`ScheduledJobBase<TStat>` lets you track your own statistics instead of the built-in `NumSucceeded`/`NumSkipped`/`NumErrors` set, by supplying your own class that inherits from `ScheduledJobStatistic`:

**Example - Track a custom counter:**

```cs
public class MyJobStats : ScheduledJobStatistic
{
    public int TotalProcessedRecords { get; set; }
}

[ScheduledJob("1FF7882E-0A3B-4F88-A8B3-C3C20AFAFBDE", StartUpMode.Manual)]
public class MyJob : ScheduledJobBase<MyJobStats>, ITransientDependency
{
    public override async Task DoExecuteAsync(CancellationToken cancellationToken)
    {
        for (var i = 0; i < 100; i++)
        {
            // JobStatistics here is typed as MyJobStats, so it has all the properties defined on that class
            JobStatistics.TotalProcessedRecords++;
        }
    }
}
```

The job's statistics can be viewed from the admin portal: after starting the job, navigate to its details page and click on a recent execution in the data table.

![image](https://user-images.githubusercontent.com/85956374/222988093-0c14e798-c7be-4212-913a-f3619c00d82c.png)

![image](https://user-images.githubusercontent.com/85956374/222988810-8836bb7e-937b-4f50-8a36-4feeb4f9cf7b.png)

---

## Responding to Job Success

`ScheduledJobBase<TStat>.MarkExecutionAsSuccessAsync()` is virtual, so you can override it to run extra logic once a job completes successfully (for example, sending a notification):

```cs
public override async Task MarkExecutionAsSuccessAsync()
{
    await base.MarkExecutionAsSuccessAsync();
    // Do something extra now that the job has succeeded
}
```

:::note No equivalent hook for failure or per-log-line events
There is currently no override point for reacting to a job failing (`MarkExecutionAsFailureAsync` is not virtual), and no per-job hook for individual log lines. Logging is handled globally through log4net rather than through the job class itself.
:::

---

## Logging

There are two ways to persist a job's log file:

1. **File system** - logs are written under the `~/App_Data` folder.
2. **Stored files** - logs are saved as Shesha stored files, which can be configured to persist to Azure storage or the `~/App_Data` folder.

Set the logging method via the `LogMode` and `LogFolder` parameters of the `[ScheduledJob]` attribute:

```cs
[ScheduledJob("1FF7882E-0A3B-4F88-A8B3-C3C20AFAFBDE", StartUpMode.Manual, LogMode = LogMode.StoredFile, LogFolder = "ThisJob/Awesome")]
public class TestJob : ScheduledJobBase, ITransientDependency
{
}
```

`LogMode` selects how logs are persisted, while `LogFolder` names the subfolder logs are stored under (inside `App_Data`, or under Azure storage when using `LogMode.StoredFile`).

All background jobs log their progress and errors to a log file via the `Log` property. By default, this is written to `\App_Data\jobs\{jobname}\{execution run log file}.log`, viewable directly on the server, or through the Azure App Service Editor if the application is hosted on Azure:

![image](https://user-images.githubusercontent.com/85956374/222988833-c1b6d4a6-6d62-4d46-9143-3fc5c20940cb.png)

Then navigate to the `App_Data\jobs\{Job name}` folder to view the various log files:

![image](https://user-images.githubusercontent.com/85956374/222988841-a457472f-ea61-41d5-9a60-3cb39b38acf5.png)

