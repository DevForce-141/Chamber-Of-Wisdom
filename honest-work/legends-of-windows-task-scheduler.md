### Source: Honest Work (Written by Myself)

## Overview
Windows Task Scheduler is well known for being stupid, just like the legacy of most Microsoft products. Here, some issues
observed in task scheduler are discussed.

### Run Task every 1 minute
There's no 1 minute frequency in the dropdown. However, you can click and edit it's contents. If you set it to
``1 minute``, your task will run every 1 minute. It's wild that you can edit dropdown content like this and Microsoft hasn't
bothered in ages to provide "1 minute" as option.

### Task does not start on it's due time
If you had scheduled a task to run indefinitely and start at a given datetime, your start date will be in past after a day.
In this case, once the task is shutdown in any way (system restart, process crashed etc.), the task will not start because
the start date is in past. A solution is provided by [This ServerFault Answer](https://serverfault.com/a/1084506).
Copy Paste of answer & screenshot is added below in case this answer is lost/removed by the time you're reading this.
```
I'm late to the party, but for anybody suffering from the same problem :
When using a Daily schedule and the Repeat task every: option in the trigger's Advanced Settings, do not use Indefinitely 
in the for a duration of: option. Change it to 1 day
This does not make any sense but solves your problem. Windows 2016 still has this problem.
```
![Answer SS](../public/images/honest-work/task-scheduler-daily-frequencey-sf-answer.png "Answer SS")
[Another answer along the same lines!](https://superuser.com/a/1819607/1685774)

### Do NOT use Conditions
It is easy to mistake them as extra actions to ensure task runs. For example "wake computer to run task". However, small
text on the top says that alongside trigger, these conditions must be true as well; else, task will not run.
![Task Scheduler Conditions](../public/images/honest-work/task-scheduler-conditions.png "Task Scheduler Conditions")

### Windows Task Scheduler x Laravel Scheduler
For this approach, schedule a task to run every minute which runs laravel scheduler. Please note that it will spawn a process
which is similar to when you run a laravel command in CLI. However, the difference is that it is blocking. It will not run
other scheduled commands except the first one (not well tested but it is observed at least some other commands do not run).
For this, you will need to run your commands in background mode using ``runInBackground()``.
Given your scheduler is running every minute, there are chances of task overlapping, so you should use ``withoutOverlapping()``
to prevent running one task multiple times. Task overlocking uses a cache lock which defaults to 24 hours. So based on your
scheduled command, set a different expiry time by providing it as parameter to the function. If you're running your command
every 5 minutes, you should set the expiry lock to 5 minutes.
Currently, I don't know & don't understand how a lock can get stuck and prevent further scheduled executions but what I have
found out via observation is that you need to specify lock expiry time if your command runs again in less than 24hrs as
default expiry time is ``1440mins (24hrs)``.
