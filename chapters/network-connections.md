Network Connections
===================

Sysmon will log **EventID 3** for all TCP and UDP network connections. This event will generate a large number of entries and filtering should be tuned for specific processes and ports. 

For the DestinationHostname, the GetNameInfo API is used and it will often not have any information and may just be a CDN, making it NOT reliable for filtering since it uses a reverse DNS Lookup to get this information, in Sysmon v11.0 this behaviour can be disabled by using the ```<DnsLookup>False</DnsLookup>``` at the root of the configuration file. 

For the DestinationPortName, the GetNameInfo API is used for the friendly name of ports. In the case of services doing connections on some systems due to memory use, they are hosted under svchost.exe and most connections will originate from this process.

The fields for the event are:

* **RuleName**: Name of rule that triggered the event

* **UtcTime**: Time in UTC when event was created

* **ProcessGuid**: Process GUID of the process that made the network connection

* **ProcessId**: Process ID used by the OS to identify the process that made the network connection

* **Image**: File path of the process that made the network connection

* **User**: Name of the account who made the network connection

* **Protocol**: Protocol being used for the network connection

* **Initiated**: Indicated process-initiated TCP connection

* **SourceIsIpv6**: Is the source IP an Ipv6

* **SourceIp**: Source IP address that made the network connection

* **SourceHostname**: DNS name of the host that made the network connection

* **SourcePort**: Source port number

* **SourcePortName**: Name of the source port being used

* **DestinationIsIpv6**: Is the destination IP an Ipv6

* **DestinationIp**: IP address destination

* **DestinationHostname**: DNS name of the host that is contacted

* **DestinationPort**: Destination port number

* **DestinationPortName**: Name of the destination port


Example tracking connections for attacker "Living off the land"

```xml
<Sysmon schemaversion="4.22">
   <EventFiltering>
 <RuleGroup name="" groupRelation="or">
      <NetworkConnect onmatch="include">
            <!--Native Windows tools - Living off the land-->
            <Image name="technique_id=T1053,technique_name=Scheduled Task" condition="image">at.exe</Image> <!--Microsoft:Windows: Remote task scheduling | Credit @ion-storm -->
            <Image name="technique_id=T1218,technique_name=Signed Binary Proxy Execution" condition="image">certutil.exe</Image> <!--Microsoft:Windows: Certificate tool can contact outbound | Credit @ion-storm and @FVT [ https://twitter.com/FVT/status/834433734602530817 ] -->
            <Image condition="image">cmd.exe</Image> <!--Microsoft:Windows: Command prompt-->
            <Image name="technique_id=T1218,technique_name=Signed Script Proxy Execution" condition="image">cscript.exe</Image><!--Microsoft:WindowsScriptingHost: | Credit @Cyb3rOps [ https://gist.github.com/Neo23x0/a4b4af9481e01e749409 ] -->
            <Image condition="image">java.exe</Image> <!--Java: Monitor usage of vulnerable application | Credit @ion-storm -->
            <Image name="technique_id=T1170,technique_name=Mshta" condition="image">mshta.exe</Image><!--Microsoft:Windows: HTML application executes scripts without IE protections | Credit @ion-storm [ https://en.wikipedia.org/wiki/HTML_Application ] -->
            <Image name="technique_id=T1218,technique_name=Signed Binary Proxy Execution" condition="image">msiexec.exe</Image> <!--Microsoft:Windows: Can install from http:// paths | Credit @vector-sec -->
            <Image name="technique_id=T1069,technique_name=Permission Groups Discovery" condition="image">net.exe</Image> <!--Mitre T1018--><!--Mitre T1077--><!--Mitre T1087--><!--Mitre T1135--><!--Mitre T1069--><!--Mitre T1016--><!--Microsoft:Windows: "net use"/"net view" used by attackers to surveil and connect with file shares from command line | Credit @ion-storm -->
            <Image name="technique_id=T1218,technique_name=Signed Binary Proxy Execution" condition="image">notepad.exe</Image> <!--Microsoft:Windows: [ https://blog.cobaltstrike.com/2013/08/08/why-is-notepad-exe-connecting-to-the-internet/ ] -->
            <Image name="technique_id=T1218,technique_name=Signed Binary Proxy Execution" condition="image">powershell.exe</Image><!--Microsoft:Windows: PowerShell interface-->
            <Image name="technique_id=T1012,technique_name=Query Registry" condition="image">reg.exe</Image> <!--Mitre T1012--><!--Mitre T1112--><!--Microsoft:Windows: Remote Registry | Credit @ion-storm -->
            <Image name="technique_id=T1218,technique_name=Regsvr32" condition="image">regsvr32.exe</Image><!--Microsoft:Windows: [ https://subt0x10.blogspot.com/2016/04/bypass-application-whitelisting-script.html ] -->
            <Image name="technique_id=T1085,technique_name=Rundll32" condition="image">rundll32.exe</Image><!--Microsoft:Windows: [ https://blog.cobaltstrike.com/2016/07/22/why-is-rundll32-exe-connecting-to-the-internet/ ] -->
            <Image name="technique_id=T1031,technique_name=Modify Existing Service" condition="image">sc.exe</Image> <!--Microsoft:Windows: Remotely change Windows service settings from command line | Credit @ion-storm -->
            <Image name="technique_id=T1047,technique_name=Windows Management Instrumentation" condition="image">wmic.exe</Image> <!--T1047--><!--Mitre T1135--><!--Microsoft:WindowsManagementInstrumentation: Credit @Cyb3rOps [ https://gist.github.com/Neo23x0/a4b4af9481e01e749409 ] -->
            <Image name="technique_id=T1218,technique_name=Signed Script Proxy Execution" condition="image">wscript.exe</Image> <!--Microsoft:WindowsScriptingHost: | Credit @arekfurt -->
            <Image condition="image">driverquery.exe</Image> <!--Microsoft:Windows: Remote recognisance of system configuration, oudated/vulnerable drivers -->
            <Image condition="image">dsquery.exe</Image> <!--Microsoft: Query Active Directory -->
            <Image condition="image">hh.exe</Image> <!--Microsoft:Windows: HTML Help Executable, opens CHM files -->
            <Image condition="image">infDefaultInstall.exe</Image> <!--Microsoft: [ https://github.com/huntresslabs/evading-autoruns ] | Credit @KyleHanslovan -->
            <Image condition="image">javaw.exe</Image> <!--Java: Monitor usage of vulnerable application and init from JAR files -->
            <Image condition="image">javaws.exe</Image> <!--Java: Monitor usage of vulnerable application and init from JAR files -->
            <Image name="technique_id=T1031,technique_name=Modify Existing Service" condition="image">mmc.exe</Image> <!--Microsoft:Windows: -->
            <Image name="technique_id=T1218,technique_name=Signed Binary Proxy Execution" condition="image">msbuild.exe</Image><!--Microsoft:Windows: [ https://www.hybrid-analysis.com/sample/a314f6106633fba4b70f9d6ddbee452e8f8f44a72117749c21243dc93c7ed3ac?environmentId=100 ] -->
            <Image name="technique_id=T1016,technique_name=System Network Configuration Discovery" condition="image">nbtstat.exe</Image> <!--Microsoft:Windows: NetBIOS statistics, attackers use to enumerate local network -->
            <Image name="technique_id=T1069,technique_name=Permission Groups Discovery" condition="image">net1.exe</Image> <!--Mitre T1018--><!--Mitre T1077--><!--Mitre T1087--><!--Mitre T1135--><!--Mitre T1069--><!--Mitre T1016--><!--Microsoft:Windows: Launched by "net.exe", but it may not detect connections either -->
            <Image name="technique_id=T1018,technique_name=Remote System Discovery" condition="image">nslookup.exe</Image> <!--Microsoft:Windows: Retrieve data over DNS -->
            <Image name="technique_id=T1057,technique_name=Process Discovery" condition="image">qprocess.exe</Image> <!--Microsoft:Windows: [ https://www.first.org/resources/papers/conf2017/APT-Log-Analysis-Tracking-Attack-Tools-by-Audit-Policy-and-Sysmon.pdf ] -->
            <Image name="technique_id=T1057,technique_name=Process Discovery" condition="image">qwinsta.exe</Image> <!--Microsoft:Windows: Remotely query login sessions on a server or workstation | Credit @ion-storm -->
            <Image name="technique_id=T1121,technique_name=Regsvcs/Regasm" condition="image">regsvcs.exe</Image> <!--Microsoft:Windows: [ https://www.hybrid-analysis.com/sample/3f94d7080e6c5b8f59eeecc3d44f7e817b31562caeba21d02ad705a0bfc63d67?environmentId=100 ] -->
            <Image name="technique_id=T1057,technique_name=Process Discovery" condition="image">rwinsta.exe</Image> <!--Microsoft:Windows: Disconnect remote sessions | Credit @ion-storm -->
            <Image name="technique_id=T1053,technique_name=Scheduled Task" condition="image">schtasks.exe</Image> <!--Microsoft:Windows: Command-line interface to local and remote tasks -->
            <Image name="technique_id=T1089,technique_name=Disabling Security Tools" condition="image">taskkill.exe</Image> <!--Microsoft:Windows: Kill processes, has remote ability -->
            <Image name="technique_id=T1057,technique_name=Process Discovery" condition="image">tasklist.exe</Image> <!--Microsoft:Windows: List processes, has remote ability -->
      <Image name="technique_id=T1218,technique_name=Signed Binary Proxy Execution" condition="image">replace.exe</Image>
    </NetworkConnect>
</RuleGroup>
</EventFiltering>
</Sysmon>
```
