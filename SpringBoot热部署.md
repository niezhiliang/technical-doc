## IDEA springboot项目热部署设置

### 1.首先项目pom.xml依赖 `spring-boot-devtools`

- pom依赖

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional> true </optional>
            <scope>runtime</scope>
        </dependency>

   

- application.yml进行devtools配置

        
        spring:
          devtools:
            restart:
              # 默认情况下 resources 下的文件是不会检测更新的，这一样设置就会检测啦
              exclude: static/**,public/**
              enabled: true
                

### 2.首先将项目设置为自动加载



