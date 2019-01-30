## Version release
### Version No.

Major, minor, and revision version numbers are used. For example 2.0.0.

Refer to: [http://semver.org/lang/zh-CN/](http://semver.org/lang/zh-CN/).

- Major version number: All versions within a major version number must be compatible with each other. They are not necessarily compatible with other major versions. However, it is best to be downward compatible.
- Minor version number: represents feature enhancement. The larger the version number, more features it has.
- Revision version number: represents the BugFix version. Such versions are only used for bug fixing. The larger the version number, the more stable the application.

### Version maintenance

At most two versions can be maintained simultaneously.

For example, if the current major version is `2.2.0`, the BugFix version `2.1.x` will be maintained and bugs in version `2.0.x` will no longer be fixed and a version upgrade is recommended.

### Release process

- Daily development uses the SNAPSHOT version, such as `2.0.0-SNAPSHOT`.
- When the modified version is officially released, the version number is revised to a formal version, such as `2.0.0`.
- After release, the next version is pulled up, for example, `2.1.0-SNAPSHOT`.
