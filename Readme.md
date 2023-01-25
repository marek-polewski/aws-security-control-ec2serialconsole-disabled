This repository contains a Cloud Formation template that leverages AWS Config, SSM, and Event Bridge to ensure ec2-serial-console is disabled.
Please note that 'ec2 serial console' setting is regional, therefore you should deploy this stack through StackSets in all regions you use.
We assume AWS Config is already enabled in your account.

The stack provides the following features:
* If ec2 serial console is enabled in the current region AWS Config rule will go into NON_COMPLIANT state.
* Changing the rule state to NON_COMPLIANT will trigger an SNS notification if topic arn was provided.
* If enabled in params, changing state to NON_COMPLIANT will trigger auto-remediation with AWS SSM.
* If enabled in params, anyone calling ec2:EnableSerialConsoleAccess will trigger SNS notification and the same auto-remediation.
* If lambda implementing control will raise an error, CW alarm will be triggered assuming dynamic dashboard listing alarms are present.
* Resources supporting tagging have been tagged for later lockdown either with SCP or permission boundaries.

As SNS supports cross-region delivery, the same topic can be used in all regions. Please remember to add "events.amazonaws.com" principal to SNS topic Access Policy.


Config will notice a problem after a specified check period (at least 1h). The event-based approach will react in around 20 seconds.

If your accounts are in AWS organizations, you can use scp-ec2-serial-console-deny.yaml to block EnableSerialConsoleAccess actions. There are ways to go around SCPs, so monitoring with remediation action is still needed. 