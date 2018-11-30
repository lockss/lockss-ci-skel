# Adding Travis CI to Maven Central Support

To setup a travis-ci build in a new project

Install the travis command-line and login to your travis-ci account.

``` console
$ gem install travis
$ travis login
$ travis enable -r lockss/<new-repository>
  lockss/<new-repository>: enabled :)
```
``` console
$ git clone git://github.com/lockss/lockss-ci-skel.git
$ cd lockss-ci-skel
$ cp -r ci <path to your project>/
$ cp .travis.yml <path to your project>/
$ cd <path to your project>
```
For most lockss projects you will need to export the lockss buildmaster signing key.
Encrypt it with the travis-ci key making note of the 'openssl aes256' value output after the encrypt-file.  Replace SOME value in the before_deploy.sh script with the one from encrypt-file response.

``` console
$ gpg --export --armor your@email.com > codesigning.asc
$ gpg --export-secret-keys --armor your@email.com >> codesigning.asc
$ travis encrypt-file codesigning.asc --add
	...openssl aes256-cbc -K...
$ mv codesigning.asc.enc .ci/
$ shred --remove codesigining.asc
```
Encryption keys are unique to the project. So each project needs to generate the variables:

``` console
$ travis encrypt --add -r lockss/<repository> SONATYPE_USERNAME=<sonatype username>
$ travis encrypt --add -r lockss/<repository> SONATYPE_PASSWORD=<sonatype password>
$ travis encrypt --add -r lockss/<repository> GPG_KEYNAME=<gpg keyname>
$ travis encrypt --add -r lockss/<repository> GPG_PASSPHRASE=<gpg passphrase>

if the project supports DOCKER add the following as well.

$ travis encrypt --add -r lockss/<repository> DOCKER_USERNAME=<docker user>
$ travis encrypt --add -r lockss/<repository> DOCKER_PASSPHRASE=<docker passphrase>
```
You should now have a series of 6 entries like this in the .travis.yml:
<pre>
env:
  global:
  - secure: p8uEj2KiqIfURBji6JRusP6jkTN9393xduLM8wJ...
  - secure: c0EvMYTkmNqu0T5q/1BTHTbsTjkFfH0iU3/Da7C...
  - secure: JB31VmNYEyuqFaXjOvlELUO7MbH7W5XyI8TUQz4...
  - secure: t1+V3OfM1EiJP+F2jjXHoix83Gq8rBaqfWx9x6p...
  - secure: 2Kseu5NYEyuqFaXjOvlELUO7MkjsIEnl2mTUQz4...
  - secure: x1aVmOfM1EiJP+surn2mdMSIEHslkgh193maizh...
</pre>


