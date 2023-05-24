# General

- First, and most importantly, make sure you are in the right place.
There is more than one Docker plugin for Jenkins, and raising an issue about one plugin in the issues area for another plugin causes a lot of confusion.
See the [README](README.md) file for details.
- Please do not use the issue tracker to ask questions.
This facility is for reporting (and fixing) bugs in the code.
If you are not 100% sure it is a bug in the code, then please seek help elsewhere.
e.g. the [jenkins-users google group](https://groups.google.com/forum/#!forum/jenkinsci-users).
- [RTFM](https://en.wikipedia.org/wiki/RTFM).
The Jenkins UI pages include help that should explain things.
The
[plugin documentation](README.md)
gives additional information.
Both of these assume a basic working understanding of Docker itself, so make sure you read [the Docker documentation](https://docs.docker.com/) as well.
- Be helpful and make no demands.
  * This code is free and open-source software (FOSS). Nobody is obliged to make things work for you, *but* you have legal permission to fix things yourself.
  * If you are reporting and fixing an issue yourself, then you only need to explain what issue you are fixing in enough detail that the maintainer(s) can understand why your changes are in the public interest.
  * If you are relying on someone else to fix an issue, then you should to make it as easy as possible for others to fix it for you, and you should test any fixes provided.

# Legal conditions

- Any contributions (code, information etc) submitted will be subject to the same [license](LICENSE) as the rest of the code.
No new restrictions/conditions are permitted.
- As a contributor, you MUST have the legal right to grant permission for your contribution to be used under these conditions.

# Reporting a new issue

- Provide a full description of the issue you are facing.
  * What are you trying to do?
  * How is this failing?
  * What should happen instead?
- Provide step-by-step instructions for how to reproduce the issue.
  * Try to avoid relying on custom Docker images or for the reproduction case. Ideally, reproduce with a `jenkins/(ssh-agent|inbound-agent|agent)` image with a freestyle job, as that makes life easier for everyone.
- Specify the Jenkins core & plugin version (of all Docker-related plugins) that you are seeing the issue with.
- Check **Manage, Manage Old Data** for out of date configuration data and provide this info.
- Check and provide errors from the system `jenkins.log` and errors from **Manage, System Log**.
  * If that log is too verbose, create a `docker-plugin` log that only logs `com.nirima.jenkins.plugin.docker` and `io.jenkins.docker` (e.g. at level "ALL") and grab what is logged when you reproduce the issue.
  * Exceptions and stack traces are *especially* useful, so do not omit those...
  * ...except please note that exceptions logged by the `docker-java` library are "expected behaviour" (include those, but do not worry about them).
  * Note that sensitive information may be visible in logs, so take care to redact logs where necessary.
- Provide a copy/paste of the cloud section from `$JENKINS_HOME/config.xml` file (redacted where neccessary).
- Describe what your Docker infrastructure looks like, e.g. single vs multiple hosts, where the Jenkins controller is in relation to the Docker host, etc.
- Provide Docker Engine version and API version.
- Ensure that any code or log examples are surrounded with [the right Markdown](https://help.github.com/articles/github-flavored-markdown/); otherwise, it will be unreadable.

# Submitting pull requests

- A PR's description must EITHER refer to an existing issue (either in GitHub or Jira) OR include a full description as for "Creating new issue".
- A single PR should EITHER be making functional changes OR making (non-functional) cosmetic/refactoring changes to the code.
Please do not do both in the same PR as this makes life difficult for anyone else merging your changes.
- For functional-change PRs, keep changes to a minimum (to make merges easier).
- Coding style:
  * Try to fit in.
  Not all of the code within this plugin is written in the same "style".
  Any changes you make must fit in with the existing style that is prevalent within the area in which you are working.
  i.e. code in the same style that the existing method/class/package uses.
  * If you cannot tolerate inconsistencies, submit a cosmetic PR that applies the formatting/whitespace/non-functional changes that you want made.
  * If in doubt, use 4 spaces for indentation, avoid using tabs, avoid trailing whitespace, use unix end-of-line codes (LF, not CR/LF), and make sure every file ends with a newline.
- Unit tests:
  * Any new functionality must be unit tested.
  * PRs that add unit-tests for existing functionality will be very welcome as well.
  * Unit tests should be as fast as possible but *must* be reliable. Tests that behave inconsistently cause issues for everyone else trying to work on the code.
- Clean build & test:
  * Any submitted PRs should automatically get built and tested; PRs will not be considered for merger until they are showing a clean build.
  If you believe that the build failed for reasons unconnected to your changes, close your PR, wait 10 minutes, then re-open it (just re-open the same PR; do not create a new one) to trigger a rebuild.
  Repeat until it builds clean, changing your code if necessary.
  * Please provide unit-tests for your contribution.
  * Do not give SpotBugs, the compiler, or any IDEs anything new to complain about.
- Preserve existing functionality by default:
  * Where possible, ensure that existing users do not find that their functionality has changed after they have upgraded from an old version of the plugin to a version of the plugin that contains your changes.
  * When adding new functionality, try to keep the defaults are unchanged so that nobody finds new, unexpected, things happening after upgrading.
  * Ensure any breaking changes are well documented in the PR description.
- Configuration data changes:
  * If the PR adds a new field to an existing (or new) data structure, make sure you have written good help text for it that explains what it is for, why it is useful, and an example.
  Implement corresponding `doCheck` methods to provide validation when users are entering this data from the Web UI.
  * If the PR changes an existing field, make sure that the code copes with reading in data from older versions of the plugin.

# Links

- [Docker Plugin](https://plugins.jenkins.io/docker-plugin/)
- [Contribution Guidelines](https://www.jenkins.io/participate/code/)
