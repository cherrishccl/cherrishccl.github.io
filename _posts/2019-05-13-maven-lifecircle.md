# Maven生命周期
```
A Build Lifecycle is Made Up of Phases.A Build Phase is Made Up of Plugin Goals.
用了很久的maven了，直到最近才发现自已对maven的生命周期都还不了解，以前一直理解错了，一直把default的一些phases当成了maven的生命周期。现记录一下：Maven的生命周期就是对所有的构建过程进行抽象和统一。包含了项目的清理、初始化、编译、测试、打包、集成测试、验证、部署和站点生成等几乎所有的构建步骤。Maven的生命周期是抽象的，即生命周期不做任何实际的工作，实际任务由插件完成，类似于设计模式中的模板方法。
Maven有三套相互独立的生命周期，分别是clean、default和site。每个生命周期包含一些阶段（phase），阶段是有顺序的，后面的阶段依赖于前面的阶段。
```
![maven-lifecircle](maven-lifecircle.png)
## 生命周期
### Clean
* pre-clean
* `clean`
* post-clean
### Default
* validate
* initialize
* generate-sources
* process-sources
* generate-resources
* process-resources
* `compile`
* process-classes
* generate-test-sources
* process-test-sources
* generate-test-resources
* process-test-resources
* test-compile
* process-test-classes
* `test`
* prepare-package
* `package`
* pre-integration-test
* integration-test
* post-integration-test
* verify
* `install`
* deploy
### Site
* pre-site
* site
* post-site
* site-deploy
## 命令行和生命周期
各个生命周期相互独立，一个生命周期的阶段前后依赖。如：

```mvn clean```
调用clean生命周期的clean阶段，实际执行pre-clean和clean阶段

```mvn test```
调用default生命周期的test阶段，实际执行test以及之前所有阶段

```mvn clean install```
调用clean生命周期的clean阶段和default的install阶段，实际执行pre-clean和clean，install以及之前所有阶段