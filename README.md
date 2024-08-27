# code-generator
[Notion](https://www.notion.so/K8s-812b9655baea4593a11d3d0b608f60e7?pvs=4#18e0a8e92d2347be875795c528439627)

[https://github.com/kubernetes/code-generator](https://github.com/kubernetes/code-generator)

[k8s学习：code-generator代码生成器的使用 - 简书 (jianshu.com)](https://www.jianshu.com/p/861924c20535)

```bash
sample-controller                                   *  modulename
    ├── go.mod                                      *  go mod init 出来的文件
    ├── go.sum                                      *  go mod init 出来的检测文件
    ├── hack                                   
    │   ├── boilerplate.go.txt                      *  operator代码框架版权信息头内容
    │   ├── tools.go                                *  用于vendor自动下载code-generator文件的配置信息
    │   ├── update-codegen.sh                       *  官方sample-controller项目的模板文件，用于填写code-generator中的相关变量
    │   └── verify-codegen.sh                       *  调用update-codegen.sh，并检测代码变化的脚本，CI过程中非常重要
    ├── pkg                                         *  标准目录
    │   ├── apis                                    *  标准目录
    │   │   └── samplecontroller                    *  group,API组
    │   │       ├── v1alpha1                        *  version,API版本
    │   │       │    ├── doc.go                     *  代码生成器模板，package相关内容，全局标签填写文件
    │   │       │    ├── types.go                   *  代码生成器模板，API类型相关内容，局部标签填写文件
    │   │       │    ├── register.go                *  注册types对应的资源到k8s的schema中
    │   │       │    └── zz_generated.deepcopy.go   *  代码生成器运行后生成的深拷贝go文件
    │   │       └── register.go                     *  定义group名字
    │   └── generated
    │       ├── clientset                           *  代码生成器生成的clientset相关代码
    │       ├── informers                           *  代码生成器生成的informers相关代码
    │       └── listers                             *  代码生成器生成的listers相关代码
    ├── vendor                                      *  vendor包管理，项目依赖包会放此处，包括code-generator
    │   ├── github.com
    │   ├── golang.org
    │   ├── gopkg.in
    │   └── k8s.io
    │       ├── apimachinery
    │       └── code-generator
    │           └── generate-groups.sh              * 代码生成器主要脚本
    ├── mian.go
    ├── controller.go
    └── manifests
        ├── app_crd.yaml                            * crd定义yaml                              
        └── example.yaml                            * crd创建yaml
```

其中红色部分为手动定义的部分，绿色部分为自动生成

较多坑点：

- 最终main.go和controller.go对应的都mian这个package，因此启动controller需要执行go run .而非go run main.go
- doc.go中需要定义group名
- types.go中定义具体的types资源
- samplecontroller下的register.go中需要修改group和version名
- update-codegen.sh需要修改以匹配具体的路径名
- controller.go中需要先等待indexer缓存同步，注意写法，需要额外在controller中定义syn变量
- 执行hack下的update-codegen.sh即可生成generated和zz_generated.deepcopy.go
- [x]  main.go和controller.go中需要注意很多具体的细节，希望使用kubebuiler可以改善
