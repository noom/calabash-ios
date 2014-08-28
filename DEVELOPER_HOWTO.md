## Gem Developer How To

### Run the Tests

##### rspec

If you are writing a new feature or updating an existing one, test it with rspec.

```
$ cd calabash-cucumber
$ be rake spec
```

##### Integration Tests

**WARNING:** The integration tests will overwrite existing calabash-cucumber/staticlib and calabash-cucumber/dylibs directories.  You have been warned.

```
# These are run from calabash-ios directory
[calabash-ios] $ script/ci/test/local-run-as-travis.rb
# Requires some configuration; see the script for details.
[calabash-ios] $ script/ci/test/xtc-submit-ci.rb
```

### CI

* https://travis-ci.org/calabash/calabash-ios
* https://travis-ci.org/calabash/run_loop
* https://travis-ci.org/calabash/calabash-ios-server
* Calabash iOS toolchain testing - http://ci.endoftheworl.de:8080/

##### 

### How to Release a New Version

_Make sure you are on the master branches of `calabash-ios-server` and `calabash-ios` and that the branches are up-to-date._

_Check CI for possible problems._

_Make sure there are no outstanding pull requests._

1. Test (see notes below)
2. **calabash-ios:** update the lib/calabash-cucumber/version `VERSION`
3. **calabash-ios:** update lib/calabash-cucumber/version `MIN_SERVER_VERSION`
4. **run-loop:** make sure that correct version has been released
5. **calabash-ios:** make sure the run-loop dependency is correct
6. **calabash-ios/calabash-cucumber:** `$ rake build_server`
7. **calabash-ios/calabash-cucumber:** `$ rake install`
8. ***Make sure your changes are pushed to GitHub.***
9. make sure changes are pushed to github
10. **calabash-ios/calabash-cucumber:** `$ rake release`

#### Testing

The integration tests delete and regenerate the `staticlib` and `dylibs` directories.  Please keep this in mind. 

Ideally you should run the rspec _and_ integration tests.  The integration tests re-run a sub-set of the rspec tests; some tests are not stable on Travis CI because of the simulator environment.

These tests will protect you from obvious mistakes, but they are incomplete.  Lean on them, but keep in mind they are a work-in-progress.

#### Rspec

```
[calabash-ios/calabash-cucumber] $ be rake spec
```

##### Integration Tests

```
[calabash-ios] $ script/ci/test/local-run-as-travis.rb

# before
1. uninstall calabash-cucumber 
2. uninstall run_loop
3. install json  # common CI problem

# install latest run-loop from master branch
4. script/ci/travis/clone-and-install-run-loop.rb

# install a set of 'fake' libraries so the gem can install locally
5. script/ci/travis/install-gem-libs.rb

# bundle install to get developer dependencies
6. script/ci/travis/bundle-install.rb

# install the gem locally with rake (not with bundle exec)
7. script/ci/travis/install-gem-ci.rb

# rspec - runs fewer tests than $ be rake spec
8. script/ci/travis/rspec-ci.rb

# unit tests - deprecated; these will be removed soon
9. script/ci/travis/unit-ci.rb

# clone the calabash-ios-server and build libraries
10. script/ci/travis/rake-build-server-ci.rb

# cucumber against all simulators; includes 1 test with sim_launcher
11. script/ci/travis/cucumber-ci.rb --tags ~@no_ci

# run some dylib tests! woot! dylibs!
12. script/ci/travis/cucumber-dylib-ci.rb
```