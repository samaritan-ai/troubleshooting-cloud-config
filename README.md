# Troubleshooting Cloud Config
Formatting problems in the customData will stop your custom configuration from running. Besides inspecting the YAML for obvious misformatting you can double check formatting using your favorite YAML editor or an online checker. I use a combo of Visual Studio Code's YAML extension and YAML LINT.

Sometimes you'll have to diagnose cloud-config problems on the nodes themselves. Some pointers to follow. After you SSH to the nodes:
* Open up /var/lib/cloud/instance/cloud-config.txt to make sure cloud init was able to transform your user data to a configuration. If the file does not contain your configuration there's probably something wrong with the YAML.
* Open up /var/lib/cloud/instance/user-data.txt and check your configuration is there. Look for formatting problems and fix them. Tools like [shyaml](https://github.com/0k/shyaml)
are invaluable to find errors in the YAML formatting.
Find the errors, then fix the problems in the template and deploy a new cluster. Re-running just the user data module of cloud-init unfortunately re-creates ```user-data.txt``` and ```cloud-config.txt``` any edits you make to those files will be lost.
  
* If there is a cloud-config.txt configuration, but the cluster doesn't come up:
    1. First check /var/log/cloud-init-output.log. Look for the line that says "finished". 
    ```
    Cloud-init v. 0.7.7 running 'modules:final' at Thu, 22 Sep 2016 20:51:32 +0000. Up 50.03 seconds.
    Cloud-init v. 0.7.7 finished at Thu, 22 Sep 2016 20:55:21 +0000. Datasource DataSourceAzureNet [seed=/dev/sr0].  Up 277.51 seconds
    ```
    
    If that line is not there, then cloud-init is still running. You may also find error messages from STDOUT there.

    2. Check /var/log/cloud-init.log. Any errors will be in the USER DATA PRVISIONING. Look for 'fail' to find the problems.

	3. If you're still having problems, inspect that each of the files in the ```write_files``` section of the ```#cloud-config``` was written properly, starting with the first file in the file and then inspecing all others.
    
    4. Finally, check if each command in the runcmd section runs correctly. You can verify that commands are running by executing them as root (sudo su -l).

