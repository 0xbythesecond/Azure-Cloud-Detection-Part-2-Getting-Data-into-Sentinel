# Part 2: Getting Data Into Sentinel
After the Sentinel Deployment, if we go to the incidents tab on the left we see we don’t have any incidents currently as there is no data being fed into sentinel. Next, we are going to utilize data connectors and create a data collection rule to bring in data from our Windows 10 VM.

<p align="center"> <img src="https://i.imgur.com/mvi2hj1.png" height="50%" width="50%" alt=" Select Add button"/></p>

Go to the Data connectors Tab. This is where we can select the type of data that we want to bring into our SIEM.

<p align="center"> <img src="https://i.imgur.com/H3jcvVE.png" height="50%" width="50%" alt=" Data Connector"/></p>

In the Search bar type in “Windows” and you will see Windows Security Events via AMA. Select that option and click “Open Connector Page”.

<p align="center"> <img src="https://i.imgur.com/crJARTo.png" height="50%" width="50%" alt=" Open Connector Page"/></p>

Click “Create data collection rule.”

<p align="center"> <img src="https://i.imgur.com/xQpXOwJ.png" height="50%" width="50%" alt="Create data collection rule"/></p>

Give your rule a name and connect to it your resource group we have used for all resources thus far.

<p align="center"> <img src="https://i.imgur.com/mBxP1v3.png" height="50%" width="50%" alt="Create rule name"/></p>

Click “Add resources”.

<p align="center"> <img src="https://i.imgur.com/1P35M0N.png" height="50%" width="50%" alt="Add Resources"/></p>

Select the Virtual Machine created in Step 2 of the project.

<p align="center"> <img src="https://i.imgur.com/Jl3auIx.png" height="50%" width="50%" alt="Select Virtual Machine"/></p>

Your Virtual Machine should now be shown.

<p align="center"> <img src="https://i.imgur.com/eqagdSc.png" height="50%" width="50%" alt="Virtual Machine shown as option to collect data"/></p>

Now we will choose the 'Collect' tab to then choose to select “All Security Events” radio button. Once the 'All Security Events' option is selected, we can now choose 'Next: Review + Create'.

<p align="center"> <img src="https://i.imgur.com/biwZLla.png" height="50%" width="50%" alt="All Security Events"/></p>

The data collection rule should have a “Validation Passed” screen.

<p align="center"> <img src="https://i.imgur.com/zbUX5ky.png" height="50%" width="50%" alt="Validation Confirmation"/></p>

Refresh the page until the “Connected” status is shown.

<p align="center"> <img src="https://i.imgur.com/Bzhnk6y.png" height="50%" width="50%" alt="Connected Status"/></p>

<hr>

Part 3: Generating Security Events
Now that our VM is connected to Sentinel and our Log Analytics Workspace, we need to transport data from our Logs. To do this, we simply need to perform some action on the Windows 10 events that will generate security alerts.

Windows keeps a record of several types of security events. These events cover several potential scenarios such as <b> privileged use, Logon events, processes, policy changes</b>, and much more.

We will now observe some Windows security events on our Virtual Machine.

Utilize the Azure Portal to navigate to the VM created earlier in the lab.

Click “Start” at the top page to turn on the VM if its not running already . Enable Just in time Access if necessary.

<p align="center"> <img src="https://i.imgur.com/kn5tCVJ.png" height="50%" width="50%" alt="Start Virtual Machine"/></p>

Under Networking, you are given a public IP. Use an RDP on your PC Client such as Remote Desktop Connection to access your VM by entering in the public IP address.

<h6>Note: You might need to refresh after starting the virtual machine to have the public IP show up.</h6>

<p align="center"> <img src="https://i.imgur.com/bJeubsk.png" height="50%" width="50%" alt="Public IP address for VM"/></p>

<p align="center"> <img src="https://i.imgur.com/Pu1t12r.png" height="50%" width="50%" alt="RDP screen shot"/></p>

From here you will be prompted to enter the username and password created when you made the VM.

Once you successfully authenticate to the virtual machine and are logged in, search for Event Viewer and open the program.

As you can see there are several types of logs Windows Collects:
<br />
<b> Application logs, Security Logs, Setup, System</b>, and <b>Forwarded Events</b>.

<p align="center"> <img src="https://i.imgur.com/5AjVv7E.png" height="50%" width="50%" alt="Event Viewer Search"/></p>

<p align="center"> <img src="https://i.imgur.com/OnglJ9P.png" height="50%" width="50%" alt="Event Viewer"/></p>

 Our focus in this lab will be on Windows Security events.

Click “Security” and observe the events.

As you can see there are several security events in event viewer. Let’s drill into one of these events.

Use the find option and search for <b>4624</b>

<p align="center"> <img src="https://i.imgur.com/DdN1jtO.png" height="50%" width="50%" alt="Find Event 4624"/></p>

When we select event 4624 we see that 4624 ID is indicative of a successful logon. We can also examine more detailed information about the logon if need be.

<p align="center"> <img src="https://i.imgur.com/O99FaMQ.png" height="50%" width="50%" alt="4624 Log Details"/></p>

<hr> 
<b>Part 4: Kusto Query Language</b>
<br />
The purpose of a SIEM such as Azure Sentinel would be to bring data like this into a centralized location. In an enterprise, we would want data coming from all our endpoints and virtual machines to make it easier for an analyst to get the information that is needed quickly.

Let’s go back to Azure Sentinel and pull this event from our Sentinel Logs.

In the Sentinel, Main Page click “Logs”

<p align="center"> <img src="https://i.imgur.com/Djj1x5z.png" height="50%" width="50%" alt="Select Logs from Left Pane"/></p>

Logs should bring up this page.

<p align="center"> <img src="https://i.imgur.com/l06AUNY.png" height="50%" width="50%" alt="Log page"/></p>

In the section where it says “Type your query here or click one of the queries to start” we are going to use the following KQL (Kusto Query Language) Logic:

<p align="center"> <img src="https://i.imgur.com/WqG4C9y.png" height="50%" width="50%" alt="KQL command request"/></p>

Every SIEM has a search language that makes it simple to extract data from Logs. In Sentinel, that language is called <b>KQL</b> or <b>Kusto Query Language</b>. While there are many different syntax rules and ways to construct queries in KQL we will be using a few basic KQL commands to extract the data and write our analytics rule later in the lab.

Let’s break down the meaning of this query

<b> Security Event </b> refers to the event table we are pulling the data from. All the events we observed in the event viewer are stored there.

<b>Where</b> command filters on a specific category. In this case, we only want events that correspond to successful logins.

<b>Project</b> command will specify what data to display when the query is run so, in this specific scenario, we want to only see the time the logon event occurred, what computer it came from and what account on this computer-generated the event.

When the query is run we get this result.

<p align="center"> <img src="https://i.imgur.com/7BPMbr5.png" height="50%" width="50%" alt="After running KQL request"/></p>

As you can see, we have a list of all the times we have had a successful login on our VM. However, as you can see the Account Name field is empty and sentinel is not automatically putting that data into that field. We will go over how to populate that field later in the lab when we create our analytic rule.

<hr>
<b> Part 5: Writing Analytic Rule and Generating Scheduled Task </b>
<br />
We can have the option to be alerted to certain events by setting up analytic rules. The Analytic rule will check our VM for the activity that matches the rule logic and generate an alert any time that activity is observed. There will be so some details provided in the alert that can help an analyst start their investigation into determining whether the event in the alert is a false positive or true positive.

<p align="center"> <img src="https://i.imgur.com/CX6uQas.png" height="50%" width="50%" alt="Aanlytics"/></p>

These are a list of alerts that we can enable our SIEM to monitor that come out of the box. If you wish, you can expand some and see what they are comprised of by clicking create a rule and following the onscreen tasks to see the rule logic and as well as enabling the rule. However, the rules will only fire if the logic is met by a security event on your VM.

<b> Scheduled Task and Persistence Techniques </b>
<br />
The final part of this lab is to create our own custom rule to detect potentially malicious activity on our VM. In Windows Task scheduler you have the option to create a scheduled task. A scheduled task is essentially a way to automate certain activities on your machine .

For instance, you could set up a scheduled task that opens google chrome at a certain time every day. While many times scheduled task can be a harmless event this can also be used as a persistence technique for malicious actors.

According to the <a href="https://attack.mitre.org/techniques/T1053/">MITRE Attack Framework</a>, “Adversaries may abuse task scheduling functionality to facilitate initial or recurring execution of malicious code. Utilities exist within all major operating systems to schedule programs or scripts to be executed at a specified date and time”.

In this lab, our scheduled task will not be associated with any malicious activity as we will set up a scheduled task that opens Internet Explorer at a certain time but we will create an analytic rule to monitor for that specific action so we can be alerted to the to the activity in our SIEM in order to simulate the scenario.

The Windows Security Event ID that corresponds to scheduled task creation is 4698. However, these events are not logged by default in the Windows event viewer. To enable logging for this event we need to make some changes to the Windows Security policy in our VM.

Search for “Local Security Policy” in Windows 10 VM and expand “Advanced Audit Policy Configuration”

<p align="center"> <img src="https://i.imgur.com/9mBIBqi.png" height="50%" width="50%" alt="Advanced Policy Configuration"/></p>

Expand “System Audit Policies” and select "Object Access”. Then select the “Audit Other Object Access Event”

<p align="center"> <img src="https://i.imgur.com/FcU26Wc.png" height="50%" width="50%" alt="Audit Other Object Access"/></p>
  
Select to enable Configure the following event: “Success” and “Failure”

<p align="center"> <img src="https://i.imgur.com/eIx4HPD.png" height="50%" width="50%" alt="Enable Success & Failure"/></p>

Logging is now enabled for the scheduled task event.

<b> Creating our scheduled task </b>

To detect a scheduled task creation, we need to generate some activity in our VM.

Open Windows Task Scheduler and navigate to “Create Task” . Add a name and change the “Configure For” Operating system to Windows 10.

<p align="center"> <img src="https://i.imgur.com/VdvFJ5W.png" height="50%" width="50%" alt="Create Task"/></p>

<p align="center"> <img src="https://i.imgur.com/jfdfVaa.png" height="50%" width="50%" alt="Create Task"/></p>

Navigate to triggers and click “new” and schedule the task for a time close to your current time. Then select “OK”

<p align="center"> <img src="https://i.imgur.com/UK0PX7M.png" height="50%" width="50%" alt="Schedule time new current time"/></p>

Navigate to the action tab and select start a program.

Then open program or script and select a program to run every time this task runs. I will select Internet Explorer.

<p align="center"> <img src="https://i.imgur.com/xmFDX0E.png" height="50%" width="50%" alt="choose program"/></p>

<p align="center"> <img src="https://i.imgur.com/J5iRQzK.png" height="50%" width="50%" alt="choose program"/></p>

<p align="center"> <img src="https://i.imgur.com/NTIWyC1.png" height="50%" width="50%" alt="Select a Program"/></p>

After this do not worry about the Conditions and Settings tab. Do not change any of the additional settings and click “OK”.

This will create your scheduled task you can now go to event viewer and search for that event id 4698 in the security logs. You can see here there are several instances of this event.

<p align="center"> <img src="https://i.imgur.com/NdrpYhJ.png" height="50%" width="50%" alt="Instances of Created Event"/></p>

<b> Writing Analytic Rule </b>

Lastly, we need to write some KQL logic to alert us when a scheduled task is created.

Go to the sentinel Home Page and click “Analytics Rules” and click create at the top of the page and select the scheduled query option.

<p align="center"> <img src="https://i.imgur.com/my3tdAw.png" height="50%" width="50%" alt="Sentinel Created Rules for Scheduled Query"/></p>

Here we are simply providing some information about the alert to the analyst.

Next, we will come up with the alert logic that causes our alert to fire.

Most of the logic will be like the KQL Query that we created earlier for logon event.

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="KQL Query 4698"/></p>

This query will pull instances of scheduled task creation as shown here in our logs.

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="KQL Query 4698"/></p>

Expand the log for the scheduled task you created in the previous step and look at the Event Data category.

You should see something like this:

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Expand the Event Data"/></p>

There is a lot of useful data in here such as the name of the scheduled task, the Task Name field, the ClientProcessID, the username of the account that created the scheduled task amongst other info.

However, if you use the project command to display these data fields as columns when you run the query, we need to add this to our logic.

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Added Project Command for KQL"/></p>

The Parse command will allow us to extract data from the Event Data Field that we find important.

This extracted the SubjectUserName , TaskName, ClientProcessID (Computer automatically displays) .

The above logic allows me to assign those to new categories such as User, NameofScheduledTask, and ClientProcessID respectively. When we project our new fields, the output is the following:

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="NameofScheduledTask, and ClientProcessID"/></p>

As you can see we’re able to generate Event Data and place it into its own category for readability.

Copy and Paste the above query into the editor under the “Set Rule Logic” tab
<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Set Rule Logic"/></p>

The Alert Enrichment section is below this. Enriching an alert simply is the process of adding context to the alert to make it easier for the analyst to investigate.

As opposed to having to query this data as we did in the analytic rule Alert Enrichment allows us to put the necessary data into the alert details as Entities so the analyst can begin investigating those specific components.

Use the following settings for entity mapping.

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Entity Mapping"/></p>

The only other setting that need to be changed for the purposes of this lab is how often the query is run and when to look up the data. Change defaults to the following:

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="How often query is run"/></p>

Incident Settings and Automated Response are not necessary to alter for this lab so you can go ahead to “review and create” to make the Analytic Rule.

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Confirm the existing schedule rule "/></p>

Once you create your analytic rule the final step is to create another scheduled task in your Windows VM and wait for the alert to trigger in Sentinel

####### Note this might take up to 10 minutes. Refresh the Incidents page periodically until the Alert triggers. When the alert fires this is what you should see.

<p align="center"> <img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Second Scheduled Task"/></p>

On the right pane we see all the necessary information we would need to begin investigating the alert such as the host machine, user account, process ID of the task, and the name of the scheduled task.

While in this case, the scheduled task is non-malicious, in the event it was, from here an analyst would investigate the entities such as the user account, the scheduled task, host, etc. with other tools such as an EDR solution and other security tools to decide if this is a false positive or true positive.

<hr>

Part 6: MITRE ATT&CK
<br />
The observed MITRE ATT&CK tactic used in this lab is <a href="https://attack.mitre.org/tactics/TA0003/">TA0003 Persistence </a> which essentially allows a malicious actor to maintain a foothold in an environment.

<p align="center"> <img src="https://i.imgur.com/NyRUngl.png" height="80%" width="80%" alt="Persistence MITRE ATT&CK"/></p>

We can dig further into this by showing the technique of using a scheduled Task/Job.

<p align="center"> <img src="https://i.imgur.com/YtkBmtQ.png" height="80%" width="80%" alt="Persistence MITRE ATT&CK"/></p>

Even further, we can dig into the specific sub technique of <a href="https://attack.mitre.org/techniques/T1053/005/">T1053.005 </a>

<p align="center"> <img src="https://i.imgur.com/eYZkpCM.png" height="80%" width="80%" alt="Scheduled Task-Job"/></p>

<b> Detection </b>
<br />
As observed. monitoring and logging of specific windows event id was used to detect this activity. However, MITRE also has more recommendations for detection.

<p align="center"> <img src="https://i.imgur.com/eder4e5.png" height="80%" width="80%" alt="Scheduled Task-Job"/></p>

Mitigation
<br />
<a href="https://attack.mitre.org/mitigations/M1018//"> MITRE ID M1019 </a> – User Account Management suggests that user account privileges should be limited to only authorize admins to create scheduled tasks on remote systems.

<p align="center"> <img src="https://i.imgur.com/Uoh3uny.png" height="80%" width="80%" alt="Mitre Mitigation"/></p>







