---
title: SpringBoot自定义Starter
author: Guocl
date: 2024-06-21
category: SpringBoot
layout: post
mermaid: true
---

- 创建一个springboot 项目 引入依赖
  collapsed:: true
  - lombok 方便开发
  - [[SpringConfigurationProcessor]] 配置文件提示的作用
    collapsed:: true
    - 在开发期间能够通过ide的处理给我们更多的便捷提示
- 删除默认生产的main启动类
- 下载依赖源码 进行查看
  collapsed:: true
  - 下载源码失败
    - 由于maven的版本问题导致的源码无法下载，配置下maven的目录
- 编写逻辑
  collapsed:: true
  -
  ```java
  public interface IdGenerator {
      String nextId();
  }
  
  import cn.hutool.core.util.IdUtil;
  import com.gcl.service.IdGenerator;
  import org.springframework.stereotype.Service;
  
  @Service
  public class UUIDGeneratorImpl implements IdGenerator {
      @Override
      public String nextId() {
          return IdUtil.fastSimpleUUID();
      }
  }
  ```
- maven编译生成jar
  collapsed:: true
  - maven 编译时可以ctrl 选择多个 如clean install 一起，然后再执行
  - maven install 时，出现repackage failed: Unable to find main class
    collapsed:: true
    - 问题原因为使用spring boot项目，用的maven插件为spring-boot-maven-plugin
      collapsed:: true
      -
      ```apl
      <plugins>
                  <plugin>
                      <groupId>org.springframework.boot</groupId>
                      <artifactId>spring-boot-maven-plugin</artifactId>
                      <configuration>
                          <excludes>
                              <exclude>
                                  <groupId>org.projectlombok</groupId>
                                  <artifactId>lombok</artifactId>
                              </exclude>
                          </excludes>
                      </configuration>
                  </plugin>
              </plugins>
      ```
    - 此插件打包时候会默认寻找签名是public static void main(String[] args)的方法，找不到所以报错
    - 修改配置解决  <skip>true</skip>
      collapsed:: true
      -
      ```apl
      <plugins>
                  <plugin>
                      <groupId>org.springframework.boot</groupId>
                      <artifactId>spring-boot-maven-plugin</artifactId>
                      <configuration>
                          <excludes>
                              <exclude>
                                  <groupId>org.projectlombok</groupId>
                                  <artifactId>lombok</artifactId>
                              </exclude>
                          </excludes>
                          <skip>true</skip>
                      </configuration>
                  </plugin>
              </plugins>
      ```
    - 或者使用 修改maven插件，改用apache的maven插件
      collapsed:: true
      -
      ```apl
       <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
      </plugin>
      ```
- 测试
  collapsed:: true
  -
  ```java
  @Import(UUIDGeneratorImpl.class)
  
  ConfigurableApplicationContext context = SpringApplication.run(CustomerIdGeneratorBootStarterTestApplication.class, args);
          UUIDGeneratorImpl uuidGenerator = context.getBean(UUIDGeneratorImpl.class);
  
  
          System.out.println(uuidGenerator.nextId());
  ```
- 使用方式
  collapsed:: true
  - 手动方式
    - 一种 引入依赖，再使用@Autowired 去使用具体使用
    - 二种 再启动类中使用注解 @Import(UUIDGeneratorImpl.class)
      collapsed:: true
      - 当一个接口有多个实现类时，出现接口引入时找不到实现类
        collapsed:: true
        - 使用@Autowired 和@Qualifier("UUIDGeneratorImpl")
        - 或者使用@Resource(name = "UUIDGeneratorImpl")
        - name设置注意
          collapsed:: true
          - 如何定义了实现接口的name就按定义的，未定义需要全路径名称
          - 查找注入类的方法
            - 启动类中打印所有注入的bean名称
            ```
            String[] beanDefinitionNames = context.getBeanDefinitionNames();
            System.out.printf("names:"+ Arrays.asList(beanDefinitionNames));
            ```
      - 更完整的方案使用@Enablexxxx方案 将 @Import 包含进去
        - 参照springboot内部的其他@Enablexxxx 方法 实现自己的方法
          collapsed:: true
          - EnableCaching
          - EnableAsync
        - 例如创建注解
          collapsed:: true
          -
          ```apl
          @Target({ElementType.TYPE})
          @Retention(RetentionPolicy.RUNTIME)
          @Documented
          @Import({UUIDGeneratorImpl.class, SnowflakeGeneratorImpl.class})
          public @interface EnableIdGenerator {
          }
          ```
    - 二种进阶 @Enablexxxx
  - 自动方式
    - SPI+ xxxAutoConfiguration+@Conditionalxx 注解按需装配
      - SPI
        - 参照其他xxxAutoConfiguration 来写，同样需要在resource目录下添加 META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
        - 添加xxxAutoConfiguration 的全路径
        - 在xxxAutoConfiguration 中的bean 使用@Conditionalxx 来控制按需求添加
      - 需要使用配置提示的功能（编写配置的时候自动提示）
        - 引入spring-boot-configuration-processor依赖
        - 配置类添加getter和setter，使用@Data注解即可
        - target/META-INF目录中查看，如果出现`spring-configuration-metadata.json`这个文件，并且内容不为空就说明成功
        - 参数最好设置成枚举
