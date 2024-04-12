Summary of the 504 error incident report and site outage
The server fell down at midnight on September 20, 2023, causing a 504 error for anyone attempting to access a website. An explanation of the server's LAMP stack foundation.

Timetable
500 error for those attempting to access the website at 00:00 PST
00:05 PST: Verifying that Apache and MySQL are operational.
00:10 PST - A background check showed that the database and server were operating OK, despite the website not loading correctly.
00:12 PST-When attempting to curl the website, the Apache server returned a status of 200 and OK following a brief restart.
00:18 PST: Examining error logs to determine the possible source of the error.
00:25 PST - Verify if the Apache server was shutting down early by looking at /var/log. There was no sign of the PHP error log.
00:30 PST: We discovered that all error logging had been off by checking the php.ini settings. activating error logging.
00:32 PST: Restarting the Apache server and checking the PHP error logs to see what is being recorded there.
00:36 PST - Analyzing PHP error logs showed that a misspelled file name was causing erroneous loading and an early shutdown of Apache.
00:38 PST - Restarting Apache and correcting the file name.
00:40 PST - The website is loading correctly and the server is currently operating regularly.

The Cause and Solution
The wp-settings.php file's incorrect file name reference was the root of the problem. The server responded with an error code of 500 when the attempt to curl the server was made. It was discovered through reviewing the error logs that no error log file was being written for PHP failures, and there was little information about the server's premature shutdown to be obtained in reading the default error log for Apache. The engineer decided to check the php.ini file's error log settings after realizing that the php errors were not being sent anywhere and discovered that all error logging had been disabled. 
After being enabled, the Apache server's error logging was restarted to see if any issues were showing up in the log. The php log confirmed what was expected: the wp-settings.php file did not contain a file with the.phpp extension. This was obviously a typographical error that caused the site access error. Since the issue was only discovered in one server, it's possible that it was also duplicated in other servers. Fixing the file extension with puppet would be a simple solution that would also affect other servers. After the puppet code was quickly deployed and all misspelled file extensions were changed to the correct ones, the server was restarted, and the files loaded correctly.

Remedial and Prophylactic Actions
Error logging ought to be enabled on all servers and websites in order to quickly discover problems in the event that they arise.
Before deploying on a multi-server setup, all servers and sites should be checked locally. This will allow for the correction of issues prior to being live, cutting down on the amount of time needed to fix a downed site.
