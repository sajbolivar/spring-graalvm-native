# spring-graalvm-native

**Resumo do Build**

- **Status:** BUILD SUCCESS
- **Tempo de geração do executável nativo:** 27.1s
- **Tempo total do build (maven):** 30.990 s

**Artefatos Gerados**

- `/workspaces/demo/target/demo` — executável nativo (AOT)
- `/workspaces/demo/target/libawt.so` — jdk_library
- `/workspaces/demo/target/libawt_headless.so` — jdk_library
- `/workspaces/demo/target/libawt_xawt.so` — jdk_library
- `/workspaces/demo/target/libjava.so` — jdk_library_shim
- `/workspaces/demo/target/libjvm.so` — jdk_library_shim
- `/workspaces/demo/target/libmanagement_ext.so` — jdk_library

**Execução Observada**

- Executável nativo (`./target/demo`) iniciou a aplicação Spring Boot AOT e registrou: "Started DemoApplication in 0.029 seconds (process running for 0.031)" — startup observado muito rápido.
- JAR (`java -jar target/demo-0.0.1-SNAPSHOT.jar`) iniciou a aplicação em ~0.636 segundos (tempo de inicialização maior que o binário nativo, conforme logs).
- Aplicação usa o Tomcat embutido e escuta na porta `8080` por padrão.

**Avaliação dos Componentes**

- **Executável nativo (`target/demo`)**: Proporciona startup muito rápido (observado ~0.03s). Bom para casos em que tempo de arranque é crítico.
- **Bibliotecas JDK nativas**: Arquivos `libawt*`, `libjava.so`, `libjvm.so`, `libmanagement_ext.so` indicam inclusão de bindings/serviços nativos no artefato; aumentam o tamanho final, verifique se AWT/GUI é necessário para o seu contexto (pode ser excluído para reduzir imagem).
- **Dependências de desenvolvimento**: `spring-boot-devtools` e `spring-boot-docker-compose` estão marcadas como opcionais/`runtime` no `pom.xml` — úteis em desenvolvimento, mas normalmente removidas ou excluídas para builds de produção.
- **Testcontainers e dependências de teste**: Presentes apenas em escopo `test`, boas práticas para testes de integração.

**Recomendações / Próximos passos**

- Se o objetivo for imagem enxuta, avaliar exclusão de módulos não utilizados (AWT) antes de gerar o nativo.
- Validar endpoints da aplicação após o start com um `curl` contra a porta `8080`.
- Automatizar build nativo em CI e medir tamanhos/tempos para comparar versões.

**Comandos úteis**

```
# Executável nativo
./target/demo

# Executar JAR com JVM
java -jar target/demo-0.0.1-SNAPSHOT.jar

# Testar endpoint (exemplo)
curl http://localhost:8080/
```

**Onde ver**

- Arquivo `pom.xml` contém as configurações de AOT e plugins relacionados ao build.
- Artefatos gerados em `/workspaces/demo/target/`.

---
Gerei este resumo a partir dos logs do build e das execuções observadas.
