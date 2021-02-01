---
title: openfeign兼容netflix
tags:
---
```java
public class NetflixFeignBeanPostProcessor implements BeanFactoryPostProcessor, ResourceLoaderAware, EnvironmentAware {

    public static final String[] basePackages = new String[]{
        
    };

    private ResourceLoader resourceLoader;

    private Environment environment;

    @Resource
    private Decoder decoder;
    @Resource
    private Encoder encoder;
    @Resource
    private Client client;
    @Resource
    private Contract contract;


    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        ClassPathScanningCandidateComponentProvider scanner = getScanner();
        scanner.setResourceLoader(this.resourceLoader);
        scanner.addIncludeFilter(new AnnotationTypeFilter(FeignClient.class));

        LinkedHashSet<BeanDefinition> candidateComponents = new LinkedHashSet<>();
        for (String basePackage : basePackages) {
            candidateComponents.addAll(scanner.findCandidateComponents(basePackage));
        }
        for (BeanDefinition candidateComponent : candidateComponents) {
            if (candidateComponent instanceof AnnotatedBeanDefinition) {
                // verify annotated class is an interface
                AnnotatedBeanDefinition beanDefinition = (AnnotatedBeanDefinition) candidateComponent;
                AnnotationMetadata annotationMetadata = beanDefinition.getMetadata();
                Assert.isTrue(annotationMetadata.isInterface(),
                        "@FeignClient can only be specified on an interface");

                Map<String, Object> attributes = annotationMetadata
                        .getAnnotationAttributes(FeignClient.class.getCanonicalName());

                String name = getClientName(attributes);

                registerFeignClient(annotationMetadata, attributes, beanFactory);
            }
        }
    }

    private void registerFeignClient(AnnotationMetadata annotationMetadata, Map<String, Object> attributes, ConfigurableListableBeanFactory beanFactory) {
        String className = annotationMetadata.getClassName();
        log.info("register netflix client {}", className);
        String url = getUrl(attributes);
        String name = getName(attributes);
        if (StringUtils.isEmpty(url)) {
            url = environment.getProperty(name + ".ribbon.listOfServers");
        }
        try {
            beanFactory.registerSingleton(className, Feign.builder()
                    .contract(new SpringMvcContract())
                    .target(Class.forName(className), url));
        } catch (ClassNotFoundException e) {
            log.error(" {} class not found", className, e);
        } catch (Exception e) {
            log.warn(" {} class url is empty", className);
        }

    }


    private String getName(Map<String, Object> attributes) {
        String name = (String) attributes.get("serviceId");
        if (!StringUtils.hasText(name)) {
            name = (String) attributes.get("name");
        }
        if (!StringUtils.hasText(name)) {
            name = (String) attributes.get("value");
        }
        name = resolve(name);
        if (!StringUtils.hasText(name)) {
            return "";
        }

        String host = null;
        try {
            String url;
            if (!name.startsWith("http://") && !name.startsWith("https://")) {
                url = "http://" + name;
            } else {
                url = name;
            }
            host = new URI(url).getHost();
        } catch (URISyntaxException e) {
        }
        Assert.state(host != null, "Service id not legal hostname (" + name + ")");
        return name;
    }

    private String resolve(String value) {
        if (StringUtils.hasText(value)) {
            return this.environment.resolvePlaceholders(value);
        }
        return value;
    }

    private String getUrl(Map<String, Object> attributes) {
        String url = resolve((String) attributes.get("url"));
        if (StringUtils.hasText(url) && !(url.startsWith("#{") && url.contains("}"))) {
            if (!url.contains("://")) {
                url = "http://" + url;
            }
            try {
                new URL(url);
            } catch (MalformedURLException e) {
                throw new IllegalArgumentException(url + " is malformed", e);
            }
        }
        return url;
    }


    private String getClientName(Map<String, Object> client) {
        if (client == null) {
            return null;
        }
        String value = (String) client.get("contextId");
        if (!StringUtils.hasText(value)) {
            value = (String) client.get("value");
        }
        if (!StringUtils.hasText(value)) {
            value = (String) client.get("name");
        }
        if (!StringUtils.hasText(value)) {
            value = (String) client.get("serviceId");
        }
        if (StringUtils.hasText(value)) {
            return value;
        }

        throw new IllegalStateException("Either 'name' or 'value' must be provided in @"
                + FeignClient.class.getSimpleName());
    }

    protected ClassPathScanningCandidateComponentProvider getScanner() {
        return new ClassPathScanningCandidateComponentProvider(false, this.environment) {
            @Override
            protected boolean isCandidateComponent(
                    AnnotatedBeanDefinition beanDefinition) {
                boolean isCandidate = false;
                if (beanDefinition.getMetadata().isIndependent()) {
                    if (!beanDefinition.getMetadata().isAnnotation()) {
                        isCandidate = true;
                    }
                }
                return isCandidate;
            }
        };
    }

    @Override
    public void setEnvironment(Environment environment) {
        this.environment = environment;
    }

    @Override
    public void setResourceLoader(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }
}

```