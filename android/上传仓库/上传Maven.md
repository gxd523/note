## 上传
```
apply plugin: 'maven'

uploadArchives {
    repositories.mavenDeployer {
        repository(url: uri('../repo'))
        pom.groupId = 'com.demo'
        pom.artifactId = 'asm-plugin'
        pom.version = '1.0.0'
    }
}
```
## 使用
```
buildscript {
    repositories {
        maven {
            url uri("repo")
        }
    }
}
```