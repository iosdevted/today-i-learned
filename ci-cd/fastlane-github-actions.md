# Build and Deploy my iOS app with Github Actions and Fastlane

## Setup

```
# frozen_string_literal: true
source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem "cocoapods"
gem "fastlane"
```

For installing the bundle, I had to use the code below. simple `bundle install` didn't work.

```
bundle install --deployment
```


## References

[How to setup Fastlane and Match to release iOS apps automatically on CI/CD server](https://medium.com/revelo-tech/setting-up-automatic-ios-release-with-fastlane-and-match-on-ci-cd-server-16c3f1d79bc5)

[Github Actions and Fastlane](https://byteable.dev/build-and-deploy-your-ios-app-with-github-actions-and-fastlane-48c328cc5541)