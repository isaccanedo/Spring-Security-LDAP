## Spring Security LDAP

1. Visão Geral
Neste tutorial rápido, aprenderemos como configurar o LDAP do Spring Security.

Antes de começarmos, uma observação sobre o que é LDAP - significa Lightweight Directory Access Protocol e é um protocolo aberto e independente de fornecedor para acessar serviços de diretório em uma rede.

This module contains articles about Spring Security LDAP

2. Dependência Maven
Primeiro, vamos dar uma olhada nas dependências do maven de que precisamos:

```
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-ldap</artifactId>
</dependency>

<dependency>
    <groupId>org.apache.directory.server</groupId>
    <artifactId>apacheds-server-jndi</artifactId>
    <version>1.5.5</version>
</dependency>
```

Nota: Usamos ApacheDS como nosso servidor LDAP, que é um servidor de diretório extensível e incorporável.

3. Configuração Java
A seguir, vamos discutir nossa configuração Spring Security Java:

```
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.ldapAuthentication()
            .userSearchBase("ou=people")
            .userSearchFilter("(uid={0})")
            .groupSearchBase("ou=groups")
            .groupSearchFilter("member={0}")
            .contextSource()
            .root("dc=isaccanedo,dc=com")
            .ldif("classpath:users.ldif");
    }
}
```

Esta é, obviamente, apenas a parte relevante do LDAP da configuração

4. Configuração XML
Agora, vamos dar uma olhada na configuração XML correspondente:

```
<authentication-manager>
    <ldap-authentication-provider
      user-search-base="ou=people"
      user-search-filter="(uid={0})"
      group-search-base="ou=groups"
      group-search-filter="(member={0})">
    </ldap-authentication-provider>
</authentication-manager>
   
<ldap-server root="dc=isaccanedo,dc=com" ldif="users.ldif"/>
```

Novamente, isso é apenas parte da configuração - a parte que é relevante para o LDAP; a configuração XML completa pode ser encontrada aqui.

5. Formato de intercâmbio de dados LDAP
Os dados LDAP podem ser representados usando o formato de intercâmbio de dados LDAP (LDIF) - aqui está um exemplo de nossos dados de usuário:

```
dn: ou=groups,dc=isaccanedo,dc=com
objectclass: top
objectclass: organizationalUnit
ou: groups

dn: ou=people,dc=isaccanedo,dc=com
objectclass: top
objectclass: organizationalUnit
ou: people

dn: uid=isaccanedo,ou=people,dc=isaccanedo,dc=com
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Jim Beam
sn: Beam
uid: isaccanedo
userPassword: password

dn: cn=admin,ou=groups,dc=isaccanedo,dc=com
objectclass: top
objectclass: groupOfNames
cn: admin
member: uid=isaccanedo,ou=people,dc=isaccanedo,dc=com

dn: cn=user,ou=groups,dc=isaccanedo,dc=com
objectclass: top
objectclass: groupOfNames
cn: user
member: uid=isaccanedo,ou=people,dc=isaccanedo,dc=com
```

6. O aplicativo

Finalmente, aqui está nosso aplicativo simples:

```
@Controller
public class MyController {

    @RequestMapping("/secure")
    public String secure(Map<String, Object> model, Principal principal) {
        model.put("title", "SECURE AREA");
        model.put("message", "Only Authorized Users Can See This Page");
        return "home";
    }
}
```

7. Conclusão
Neste guia rápido de Spring Security com LDAP, aprendemos como provisionar um sistema básico com LDIF e configurar a segurança desse sistema.


### Notas

- o projeto usa Spring Boot - simplesmente execute 'SampleLDAPApplication.java' para iniciar o Spring Boot com um contêiner Tomcat e servidor LDAP integrado.
- Uma vez iniciado, abra 'http://localhost: 8080'
- Isso exibirá a página inicial publicamente disponível
- Navegue até 'Página segura' para acionar a autenticação
- Nome de usuário: 'isaccanedo', senha: 'isac'
- Isso irá autenticá-lo e exibir suas funções alocadas (conforme definido no arquivo 'users.ldif')

