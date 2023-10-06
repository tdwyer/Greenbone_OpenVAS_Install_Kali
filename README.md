# Commands to install and run Greenbone OpenVAS without errors on Kali Linux

## Upgrade Kali

* `sudo apt update`
* `sudo apt upgrade`

## You need to make Postgresql 16 run on the default port `5432`

* Edit `/etc/postgresql/15/main/postgresql.conf` change port to `port = 5433`
* Edit `/etc/postgresql/16/main/postgresql.conf` change port to `port = 5432`
* Just reboot, or run the following commands
  * Stop postgresql `sudo systemctl stop postgresql.service`
  * Stop postgresql-15 `sudo systemctl stop postgresql@15-main.service`
  * Stop postgresql-16 `sudo systemctl stop postgresql@16-main.service`
  * Start postgresql-16 `sudo systemctl start postgresql@16-main.service`

## Install Greenbone OpenVAS

* `sudo apt install gvm nsis`
* Run `sudo gvm-setup`
  * *NOTE* Take note of the UUID Password in the output for the `admin` user

* Run `sudo gvm-check-setup`
  * It should not have any errors

Check if it's running on Port 9392
  * `sudo ss -ltn4p`

* Log into the website http://localhost
  * You should be redirected from Port 80 to https://localhost:9392
  * Accept the unvalidated TLS Cert

## Fix the error `Failed to find config 'daba56c8-73ec-11df-a475-002264764cea'`

*NOTE* You will likely get the error `Failed to find config 'daba56c8-73ec-11df-a475-002264764cea'` when trying to save a new Scan Task, you need to run the following `--modify-setting` and `--modify-scanner`. I have no idea why they have it ship broken but it's been this way for years now.

The `--modify-setting` command will allow you to create a new Scan Task
The `--modify-scanner` command will allow the scan to actually work. If you don't run this command then the scan will just complete listing all hosts as down (even 127.0.0.1).

Then first try these more specific commands first
* `sudo runuser -u _gvm –- gvmd --modify-setting 78eceaec-3385-11ea-b237-28d24461215b --value dabd2710-aebd-4048-a8c7-460b60e5fa93`
* `sudo runuser -u _gvm –- gvmd --modify-scanner 78eceaec-3385-11ea-b237-28d24461215b --value dabd2710-aebd-4048-a8c7-460b60e5fa93`

If those fail  for some reason. (These are the commands I ran)
* `sudo gvmd --modify-setting 78eceaec-3385-11ea-b237-28d24461215b --value dabd2710-aebd-4048-a8c7-460b60e5fa93`
* `sudo gvmd --modify-scanner 78eceaec-3385-11ea-b237-28d24461215b --value dabd2710-aebd-4048-a8c7-460b60e5fa93`

These commands should also work and I assume `admin` can be changed to any user name or maybe that's the group name. I'm not sure.
* `sudo gvmd --modify-setting 78eceaec-3385-11ea-b237-28d24461215b --value admin`
* `sudo gvmd --modify-scanner 78eceaec-3385-11ea-b237-28d24461215b --value admin`

*NOTE* I got the error `Error in setting UUID.` while trying to run the `--modify-setting` and `--modify-scanner` commands. After restarting `gvm` I was able to run the commands.

* `sudo gvm-stop`
* `sudo gvm-start`
