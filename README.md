# keycloak-services-social-wechat-work

--- 

> Keycloak企业微信登录插件

## Build package

```shell
mvn clean install
mvn clean package -e -U
```

Keycloak 15.0.0 测试通过【感谢[potterhe](https://github.com/potterhe)适配新版】
Keycloak 23.0.5 测试通过

## Install package

### 裸机部署
```bash
# build from source
mvn clean package

# add the jar to the Keycloak server (create `providers` folder if needed)
mkdir -p $KEYCLOAK_HOME/providers/
cp target/keycloak-services-social-wechat-work.jar $KEYCLOAK_HOME/providers/

# add config page templates to the Keycloak server
cp themes/base/admin/resources/partials/realm-identity-provider-wechat-work.html $KEYCLOAK_HOME/themes/base/admin/resources/partials/
cp themes/base/admin/resources/partials/realm-identity-provider-wechat-work-ext.html $KEYCLOAK_HOME/themes/base/admin/resources/partials/

# add `<module name="org.infinispan" services="import"/>` to dependencies
sed -ie 's#<dependencies>#<dependencies><module name="org.infinispan" services="import"/>#' $KEYCLOAK_HOME/modules/system/layers/keycloak/org/keycloak/keycloak-services/main/module.xml
```

### Docker部署

```Dockerfile
FROM quay.io/keycloak/keycloak:latest as builder
# Configure as needed, e.x., database, other plugins
WORKDIR /opt/keycloak
ADD --chown=keycloak:keycloak ./keycloak-services-social-wechat-work-23.0.5.jar /opt/keycloak/providers/
ADD --chown=keycloak:keycloak ./themes/realm-identity-provider-wechat-work.html /opt/keycloak/themes/base/admin/resources/partials/
ADD --chown=keycloak:keycloak ./themes/realm-identity-provider-wechat-work-ext.html /opt/keycloak/themes/base/admin/resources/partials/
RUN /opt/keycloak/bin/kc.sh build

FROM quay.io/keycloak/keycloak:latest
COPY --from=builder /opt/keycloak/ /opt/keycloak/

ENTRYPOINT ["/opt/keycloak/bin/kc.sh"]
```

## Dev

```bash
# format code
mvn com.coveo:fmt-maven-plugin:format
```

## Reference

- Based on [jyqq163/keycloak-services-social-weixin](https://github.com/jyqq163/keycloak-services-social-weixin)
