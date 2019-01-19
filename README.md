# ![](https://raw.githubusercontent.com/joefitzgerald/nlog-targets-loggly/master/SolutionItems/NLoggly.png) nlog-targets-loggly #
An NLog Target For [Loggly](http://www.loggly.com). 

[![nlog-targets-loggly MyGet Build Status](https://www.myget.org/BuildSource/Badge/nlog-targets-loggly?identifier=01f46438-a7ab-49c7-ba53-5195726e0ec0)](https://www.myget.org/) [![NuGet](https://img.shields.io/nuget/v/NLog.Targets.Loggly.svg)](https://www.nuget.org/packages/NLog.Targets.Loggly) 

Install via [nuget](https://www.nuget.org/packages/NLog.Targets.Loggly/) with

	Install-Package nlog.targets.loggly

See the Demo project in the solution for a working example. 
Be sure to create your own config file which is not included in the repo. Try something like this:
	
	C:\nlog-targets-loggly> copy .\examples\Demo\example.loggly.user.config .\examples\Demo\loggly.user.config

## Example Config ##
This NLog target project reads the [loggly-csharp configuration](https://github.com/neutmute/loggly-csharp/), so be sure to add the Loggly config section as well as NLog config. 

See below for sample NLog config (loggly config not shown).

	<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  throwExceptions="true">
		<extensions>
		  <add assembly="NLog.Targets.Loggly" />
		</extensions>
		<variable name="DefaultLayout" value="${longdate} | ${level:uppercase=true:padding=5} | ${message} | ${exception:format=type,tostring}" />
		<variable name="AppName" value="Loggly NLog Target Demo" />
	
		<targets async="true">
		  <target name="ColorConsole" xsi:type="ColoredConsole" layout="${DefaultLayout}" />
		  <target name="Loggly" xsi:type="Loggly" layout="${message}" />
		</targets>
		<rules>
		  <logger name="*" minlevel="Info" writeTo="ColorConsole,Loggly" />
		</rules>
	</nlog>

### Tags

Loggly Tags can be added like this:

	<target name="Loggly" xsi:type="Loggly" layout="${message}">
	  <tag name="{logger}" />
	</target>

### MetaData

Loggly includes NLog LogEvent Properties automatically, but one can also add extra context like this:

	<target name="Loggly" xsi:type="Loggly" layout="${message}">
	  <contextproperty name="HostName" layout="${machinename}" />
	  <contextproperty name="ThreadId" layout="${threadid}" />
	</target>

### MappedDiagnosticsLogicalContext (MDLC)

Loggly can also include async context from NLog MDLC (Contains state from NetCore ILogger.BeginScope)

	<target name="Loggly" xsi:type="Loggly" layout="${message}" includeMdlc="true" />

### Suppression
Sometimes you might emit something to a flat file log that doesn't make sense in loggly, such as a delimiting line of dashes: ---------

Add a property to your nLog event with the name `syslog-suppress` to filter these out so they don't transmit to loggly.
