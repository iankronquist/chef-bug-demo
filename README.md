# create-cookbook

This cookbook demonstrates a bug in chef's git provider.
Most of this repo is boilerplate, the only interesting file is the default
recipe: `recipes/default.rb`.


Bug report:
https://github.com/chef/chef/issues/3025

PR to potentially fix:
https://github.com/chef/chef/pull/3289



To reproduce, just run test kitchen and see it fail. 

```
	$ kitchen test
```


There is a sample log in the file
`test-kitchen-run-errors.txt`. The log contains terminal color codes, so it's
prettier if you cat it.

```
	$ cat test-kitchen-run-errors.txt
```



# The error:

```
       Recipe: create::default
  * git[/opt/fenestra] action sync       [2015-06-20T16:22:12+00:00] INFO: Processing git[/opt/fenestra] action sync (create::default line 22)
       [2015-06-20T16:22:13+00:00] INFO: git[/opt/fenestra] cloning repo https://github.com/osuosl/fenestra to /opt/fenestra
       
           
- clone from https://github.com/osuosl/fenestra into /opt/fenestra       
           ================================================================================
           Error executing action `sync` on resource 'git[/opt/fenestra]'
           ================================================================================
           
       
           Mixlib::ShellOut::ShellCommandFailed
           
       ------------------------------------
           
       Expected process to exit with [0], but received '128'
           ---- Begin output of git branch -f master cbc164c3b4f65ceb70ec0bfd404f2ef71c635583 ----
           STDOUT: 
           STDERR: fatal: Cannot force update the current branch.
           
       ---- End output of git branch -f master cbc164c3b4f65ceb70ec0bfd404f2ef71c635583 ----
           Ran git branch -f master cbc164c3b4f65ceb70ec0bfd404f2ef71c635583 returned 128
           
           
       Resource Declaration:
           ---------------------
           # In /tmp/kitchen/cookbooks/create/recipes/default.rb
           
            22: git '/opt/fenestra' do
           
        23:   action :sync
            24:   repository 'https://github.com/osuosl/fenestra'
            25:   checkout_branch 'master'
           
        26: end 
           
           
       Compiled Resource:
           ------------------
           # Declared in /tmp/kitchen/cookbooks/create/recipes/default.rb:22:in `from_file'
           
           
       git("/opt/fenestra") do
             action [:sync]
             retries 0
           
         retry_delay 2
             default_guard_interpreter :default
             destination "/opt/fenestra"
           
         enable_checkout true
             revision "HEAD"
             remote "origin"
           
         checkout_branch "master"
             declared_type :git
             cookbook_name :create
       
             recipe_name "default"
             repository "https://github.com/osuosl/fenestra"
           
       end
           
       
       [2015-06-20T16:22:15+00:00] INFO: Running queued delayed notifications before re-raising exception
       
       Running handlers:
       [2015-06-20T16:22:15+00:00] ERROR: Running exception handlers
       Running handlers complete
       [2015-06-20T16:22:15+00:00] ERROR: Exception handlers complete
       [2015-06-20T16:22:15+00:00] FATAL: Stacktrace dumped to /tmp/kitchen/cache/chef-stacktrace.out
       Chef Client failed. 2 resources updated in 22.225000906 seconds
       [2015-06-20T16:22:15+00:00] ERROR: git[/opt/fenestra] (create::default line 22) had an error: Mixlib::ShellOut::ShellCommandFailed: Expected process to exit with [0], but received '128'
       ---- Begin output of git branch -f master cbc164c3b4f65ceb70ec0bfd404f2ef71c635583 ----
       STDOUT: 
       STDERR: fatal: Cannot force update the current branch.
       ---- End output of git branch -f master cbc164c3b4f65ceb70ec0bfd404f2ef71c635583 ----
       Ran git branch -f master cbc164c3b4f65ceb70ec0bfd404f2ef71c635583 returned 128
       [2015-06-20T16:22:15+00:00] FATAL: Chef::Exceptions::ChildConvergeError: Chef run process exited unsuccessfully (exit code 1)
>>>>>> Converge failed on instance <default-centos-66>.
>>>>>> Please see .kitchen/logs/default-centos-66.log for more details
>>>>>> ------Exception-------
>>>>>> Class: Kitchen::ActionFailed
>>>>>> Message: SSH exited (1) for command: [sudo -E chef-solo --config /tmp/kitchen/solo.rb --json-attributes /tmp/kitchen/dna.json  --log_level info]
>>>>>> ----------------------
```

## License and Authors

Written by Ian Kronquist.

Licensed under Apache 2 by the OSU Open Source Lab.
