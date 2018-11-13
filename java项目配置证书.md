### 1.准备一份xxx.jks的证书一份,放置`resources`目录下

### 2.在application.yml配置证书的一些参数，以及项目的http端口和https接口  配置如下：

        server:
            #https监听端口
            port: 8989
            custom:
              #http监听端口
              httpPort: 8898
            ssl:
              key-store: classpath:xxx.jks
              key-store-password: #证书密码
              key-store-type: JKS
              key-alias: #证书别名
              

 ### 3.pringboot 2.x代码里面对tomcat配置进行修改      
 
            @Bean
    public TomcatServletWebServerFactory servletContainer() { //springboot2 新变化

        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory() {

            @Override
            protected void postProcessContext(Context context) {

                SecurityConstraint securityConstraint = new SecurityConstraint();
                securityConstraint.setUserConstraint("CONFIDENTIAL");
                SecurityCollection collection = new SecurityCollection();
                collection.addPattern("/*");
                securityConstraint.addCollection(collection);
                context.addConstraint(securityConstraint);
            }
        };
        tomcat.addAdditionalTomcatConnectors(initiateHttpConnector());
        return tomcat;
    }

    private Connector initiateHttpConnector() {
        Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
        connector.setScheme("http");
        connector.setPort(httpPort);
        connector.setSecure(false);
        connector.setRedirectPort(httpsPort);
        return connector;
    }
              

### 3.pringboot 1.5x代码里面对tomcat配置进行修改


        @Configuration
        public class TomcatConfig {
        
            @Value("${server.custom.httpPort}")
            private Integer httpPort;
        
            @Value("${server.port}")
            private Integer httpsPort;
        
            @Bean
            public EmbeddedServletContainerFactory servletContainerFactory () {
                TomcatEmbeddedServletContainerFactory tomcat = new TomcatEmbeddedServletContainerFactory() {
                    @Override
                    protected void postProcessContext(Context context) {
                        SecurityConstraint securityConstraint = new SecurityConstraint();
                        securityConstraint.setUserConstraint("CONFIDENTIAL");
                        SecurityCollection collection = new SecurityCollection();
                        collection.addPattern("/*");
                        securityConstraint.addCollection(collection);
                        context.addConstraint(securityConstraint);
                    }
                };
        
                tomcat.addAdditionalTomcatConnectors(initiateHttpConnector());
                return tomcat;
            }
        
            private Connector initiateHttpConnector() {
                Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
                connector.setScheme("http");
                connector.setPort(this.httpPort);
                connector.setSecure(false);
                connector.setRedirectPort(this.httpsPort);
        
                return connector;
            }
        
        }
        
        
此时就大功告成啦，试试用https:xxx:8989/xxx 来访问下你的项目吧
