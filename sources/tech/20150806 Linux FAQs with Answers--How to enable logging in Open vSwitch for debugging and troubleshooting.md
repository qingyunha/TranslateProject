Translating by GOlinu！
Linux FAQs with Answers--How to enable logging in Open vSwitch for debugging and troubleshooting
================================================================================
> **Question:** I am trying to troubleshoot my Open vSwitch deployment. For that I would like to inspect its debug messages generated by its built-in logging mechanism. How can I enable logging in Open vSwitch, and change its logging level (e.g., to INFO/DEBUG level) to check more detailed debug information? 

Open vSwitch (OVS) is the most popular open-source implementation of virtual switch on the Linux platform. As the today's data centers increasingly rely on the software-defined network (SDN) architecture, OVS is fastly adopted as the de-facto standard network element in data center's SDN deployments.

Open vSwitch has a built-in logging mechanism called VLOG. The VLOG facility allows one to enable and customize logging within various components of the switch. The logging information generated by VLOG can be sent to a combination of console, syslog and a separate log file for inspection. You can configure OVS logging dynamically at run-time with a command-line tool called `ovs-appctl`.

![](https://farm1.staticflickr.com/499/19300367114_cd8aac2fb2_c.jpg)

Here is how to enable logging and customize logging levels in Open vSwitch with `ovs-appctl`.

The syntax of `ovs-appctl` to customize VLOG is as follows.

    $ sudo ovs-appctl vlog/set module[:facility[:level]] 

- **Module**: name of any valid component in OVS (e.g., netdev, ofproto, dpif, vswitchd, and many others)
- **Facility**: destination of logging information (must be: console, syslog or file)
- **Level**: verbosity of logging (must be: emer, err, warn, info, or dbg) 

In OVS source code, module name is defined in each source file in the form of:

    VLOG_DEFINE_THIS_MODULE(<module-name>);

For example, in lib/netdev.c, you will see:

    VLOG_DEFINE_THIS_MODULE(netdev);

which indicates that lib/netdev.c is part of netdev module. Any logging messages generated in lib/netdev.c will belong to netdev module.

In OVS source code, there are multiple severity levels used to define several different kinds of logging messages: VLOG_INFO() for informational, VLOG_WARN() for warning, VLOG_ERR() for error, VLOG_DBG() for debugging, VLOG_EMERG for emergency. Logging level and facility determine which logging messages are sent where.

To see a full list of available modules, facilities, and their respective logging levels, run the following commands. This command must be invoked after you have started OVS.

    $ sudo ovs-appctl vlog/list 

![](https://farm1.staticflickr.com/465/19734939478_7eb5d44635_c.jpg)

The output shows the debug levels of each module for three different facilities (console, syslog, file). By default, all modules have their logging level set to INFO.

Given any one OVS module, you can selectively change the debug level of any particular facility. For example, if you want to see more detailed debug messages of dpif module at the console screen, run the following command.

    $ sudo ovs-appctl vlog/set dpif:console:dbg 

You will see that dpif module's console facility has changed its logging level to DBG. The logging level of two other facilities, syslog and file, remains unchanged.

![](https://farm1.staticflickr.com/333/19896760146_5d851311ae_c.jpg)

If you want to change the logging level for all modules, you can specify "ANY" as the module name. For example, the following command will change the console logging level of every module to DBG.

    $ sudo ovs-appctl vlog/set ANY:console:dbg 

![](https://farm1.staticflickr.com/351/19734939828_8c7f59e404_c.jpg)

Also, if you want to change the logging level of all three facilities at once, you can specify "ANY" as the facility name. For example, the following command will change the logging level of all facilities for every module to DBG.

    $ sudo ovs-appctl vlog/set ANY:ANY:dbg 

--------------------------------------------------------------------------------

via: http://ask.xmodulo.com/enable-logging-open-vswitch.html

作者：[Dan Nanni][a]
译者：[译者ID](https://github.com/译者ID)
校对：[校对者ID](https://github.com/校对者ID)

本文由 [LCTT](https://github.com/LCTT/TranslateProject) 原创翻译，[Linux中国](https://linux.cn/) 荣誉推出

[a]:http://ask.xmodulo.com/author/nanni