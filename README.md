# SECURITY INCIDENT INVESTIGATION

## Briefing:

Late on the night of August 21st, 2026, TechCorp's customer portal logged
requests to /api/customers/export - an internal endpoint that returns
customer records. It should only ever be hit by a logged-in employee with
a legitimate business reason (Sales/Support reps looking up their own
accounts), and never in the middle of the night.

Security believes this was NOT an employee. Someone broke into an account
from outside the company and used it to pull customer data.

This folder is the raw evidence, unsorted, straight off the servers. Your
job is to find the attacker's IP address and prove it wasn't legitimate
activity.

A few things to know before you start:

* The customer portal runs behind a load balancer across TWO servers
  (web1 and web2). Requests for the same night are split across both --
  checking only one server gives you an incomplete picture.

* There is more than one suspicious-looking IP address hammering the
  login page that night. At least one of them is a security tool TechCorp
  runs itself and is expected to be there. Don't assume "the loudest IP"
  is the attacker. Check the records of allowed IPs before
  you accuse anyone.

* More than one login that night came from an unfamiliar, external IP
  address, off-hours. Not all of them are malicious -- several employees
  legitimately work remotely or travel. 

* Some of the account records in this folder are out of date. Make sure
  you're looking at current data before you draw conclusions.

## Task Description

Using the Linux commands we covered in class and the data contained in this repository, 
figure out:
1. The IP address of the attacker
2. The employee name whose account was compromized.

Support your concusions with as much evidence as you can collect. 
Record the commands you use for each step in commands.sh. You can add comments
to commands.sh, make sure to start each line of comments with `#`.

## Steps

1. Find the relevant files with records of access traffic. These files are
   typically named "access.log". Some of the files in this repo are old backups,
   and are not relevant. Record the `find` command you used in commands.sh. 

2. Combine all relevant access.log files into one, to make your investigation easier.
   Record the `cat` command you used in commands.sh.

3. Identify failed login attempts. Failed login attempts will have a status 401 and 
   will use the /login endpoint. Record the `grep` command you used. 

4. Pipe the output of your previous command as the input to `awk`. Use an `awk` command
   to narrow down the failed login attempts to those made outside of business hours (9:00-18:00).
   that were made outside of normal business hours (9:00-18:00). You may need to have two `awk` 
   commands to accomplish this, piping the output of one command as the input to the other:
   - one that splits each line by `:`
   - another one that filters by the hour being less than 9 or greater than 18
   Your command needs to print the IP address of the failed request, and the API endpoint of the
   request. This API endpoint will contain the username associated with the login, you'll need it later.

   Record the full command you used in this step in commands.sh

5. Pipe the output of the previous command as the input to `sort` and `uniq -c`, counting the number of unique
   suspicious attempts.
   Record the full command you used in this step in commands.sh

6. Modify the command from last step to look at successful logins, instead of failed ones.
   Successful logins will have status 200.

7. Check if any of the IPs with successful logins are on the allowed list. The allowed list
   is in a file that has the word scanner in it. Record the `find` command you used to locate
   the allowed list file and the `grep` commands you used in commands.sh.

8. In the last step, you eliminated some suspects. Using the remaining suspect IP list, 
   inspect the traffic coming from each suspicious IP address to eliminate more suspects.
   Recall, that the attacker made a request to /api/customers/export
   Record the commands you used in this step in commands.sh

9. Locate the file that contains employee id information. The name of this file is `accounts`
   and it has some file extension.
   Record the `find` command you used in commands.sh

10. Look up your suspect in the file you located in the previous step. Record the command
   you used in commands.sh

11. Create a file evidence.txt. In this file, name your one suspect and present supporting evidence.

12. Use git command line commands to add, commit, and push your commands.sh and evidence.txt files to the repo.
