Firstly, please do not use the issue tracker to ask questions. Rather, join the [user mailing list](https://www.jenkins.io/mailing-lists/).

If you have an issue with Docker and Jenkins, then you first need to determine which plugin you are using.
There is more than one plugin for Jenkins that provides Docker-related capabilities, and this is just one of them.
See the [README.md](README.md) file for further details.

Note: The stack trace(s) you see will also indicate which plugin is causing the issue.
If none of the classes mentioned in the exception stacktrace are classes from this plugin, then the issue is probably not originating from this plugin.

Please read [the contribution guidelines](CONTRIBUTING.md) as well, as they also have a lot of useful information that will help people help you.

If you are sure that this plugin is the source of your issue, then please report:

 - [ ] `docker-plugin` version you use
 - [ ] Jenkins version you use
 - [ ] Docker Engine version you use
 - [ ] Details of the Docker container(s) involved and details of how `docker-plugin` is connecting to them
 - [ ] Stack trace / logs / any technical details that could help diagnose this issue

Lastly, before submitting a bug report, remove this explanatory text, leaving just the important information.
