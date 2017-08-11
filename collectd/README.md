# InsightAgent: collectd
Agent Type: collectd

Platform: Linux

InsightFinder agent can be used to monitor system performance metrics on bare metal machines or virtual machines using collectd.

collectd is an open source daemon that collects statistics from a system and publishes them to insightfinder server.

Supported collectd version: collectd-5.5.2

Tested on Centos 7.

##### Instructions to register a project in Insightfinder.com
- Go to [insightfinder.com](https://insightfinder.com/)
- Sign in with the user credentials or sign up for a new account.
- Go to Settings and Register for a project under "Insight Agent" tab.
- Give a project name, select Project Type as "Private Cloud".
- View your account information by clicking on your user id at the top right corner of the webpage. Note the license key number.

##### Pre-requisites
Python 2.7.

Python 2.7 must be installed in order to run the agent. For Debian and Ubuntu, the following command will ensure that the required dependencies are present
```
sudo apt-get upgrade
sudo apt-get install build-essential libssl-dev libffi-dev python-dev wget
```
For Fedora and RHEL-derivatives
```
sudo yum update
sudo yum install gcc libffi-devel python-devel openssl-devel wget
```

##### Collectd Configuration Requirements

- collectd should be installed and running.  
For Fedora and RHEL-derivatives
```
sudo yum install epel-release
sudo yum install collectd
```

- Check collectd.conf as the following plugins should be installed and available:
```
cpu, csv, disk, interface, load, memory, processes
```
- The configuration is located in /etc/collectd.conf or /etc/collectd/collectd.conf depending on installed package. The following lines in the collectd.conf should be uncommented. See sample configuration file "collectdsample.conf" for example.
```
Interval 60
LoadPlugin cpu
LoadPlugin csv
LoadPlugin disk
LoadPlugin processes
LoadPlugin memory
LoadPlugin interface
LoadPlugin load

<Plugin cpu>
  ReportByCpu false
  ReportByState false
  ValuesPercentage true
</Plugin>

<Plugin csv>
        DataDir "${prefix}/var/lib/collectd/csv"
        StoreRates false
</Plugin>
```
- The Interval above is specified in seconds. Set it to required sampling value. It specifies how often data is collected.
- After all the changes have been made restart the collectd service.
```
sudo service collectd restart
```
##### To deploy agent on multiple hosts

- Get the deployment script from github using below command
```
wget --no-check-certificate https://raw.githubusercontent.com/insightfinder/InsightAgent/master/deployment/deployInsightAgent.sh
```
- Change permission for "deployInsightAgent.sh" to executable.
-Ensure all machines have the same login username and password.
-Obtain the IP address for every machine (or host) the InsightFinder agent will be installed on.
-Include the IP address of all hosts in hostlist.txt, entering one IP address per line.
- To deploy run the following command(The -w parameter can be used to give server url example ***-w http://192.168.78.85:8080***  in case you have an on-prem installation otherwise it is not required)
```
./deployInsightAgent.sh -n USER_NAME_IN_HOST
                        -i PROJECT_NAME_IN_INSIGHTFINDER
                        -u USER_NAME_IN_INSIGHTFINDER
                        -k LICENSE_KEY
                        -s SAMPLING_INTERVAL_MINUTE
                        -r REPORTING_INTERVAL_MINUTE
                        -t AGENT_TYPE
                        -w SERVER_URL
AGENT_TYPE is *collectd*.
```
##### To view command in terminal, run
```
./deployInsightAgent.sh
```
To validate deployment, select to enter either a password or key. For a key, enter the identity file's path name. For example: /home/insight/.ssh/id_rsa


##### To undo agent deployment on multiple hosts
- Get the script for stopping agents from GitHub using below command:
```
wget --no-check-certificate https://raw.githubusercontent.com/insightfinder/InsightAgent/master/deployment/stopcron.sh
```
and change the permissions with the command
```
 chmod 755 stopcron.sh
```
- Include IP addresses of all hosts in hostlist.txt and enter one IP address per line.
- To stop the agent run the following command
```
./stopcron.sh -n USER_NAME_IN_HOST -p PASSWORD

USER_NAME_IN_HOST - username used to login into the host machines
PASSWORD - password or name of the identity file along with path
```

##### To install agent on local machine:
1) Use the following command to download the insightfinder agent code.
```
wget --no-check-certificate https://github.com/insightfinder/InsightAgent/archive/master.tar.gz -O insightagent.tar.gz
```
Untar using this command.
```
tar -xvf insightagent.tar.gz
```

2) In InsightAgent-master directory, run the following commands to install dependencies for insightfinder agent (If -env flag is used then a seperate virtual environment is created):
```
sudo ./deployment/checkpackages.sh  
OR
./deployment/checkpackages.sh -env
```

3) Run the below command to install agent.(The -w parameter can be used to give server url example ***-w http://192.168.78.85:8080***  in case you have an on-prem installation otherwise it is not required)
```
./deployment/install.sh -i PROJECT_NAME -u USER_NAME -k LICENSE_KEY -s SAMPLING_INTERVAL_MINUTE -r REPORTING_INTERVAL_MINUTE -t AGENT_TYPE -w SERVER_URL
```
