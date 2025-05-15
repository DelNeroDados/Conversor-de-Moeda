# Conversor de Moedas Profissional

---

## 📖 Descrição do Projeto

O **Conversor de Moedas Profissional** é uma aplicação Java completa que atende tanto ao modo **console** quanto à interface **gráfica Swing**, com recursos avançados:

- **Conversão “free‐form”** por código ISO ou nome de país (PT/EN), com busca inteligente e seleção em caso de ambiguidade.
- **Presets prontos** para pares comuns (USD↔ARS, USD↔BRL, USD↔COP).
- **Salvamento de favoritos** persistente em `favorites.txt`.
- **Externalização de configuração** via `application.properties` ou variável de ambiente.
- **Logging configurável** (SLF4J + Logback) para níveis INFO/DEBUG/ERROR.
- **Tratamento robusto de erros** com _multi‐catch_ e exceções customizadas.
- **Arquitetura modular** (Separation of Concerns) para facilitar manutenção e testes.
- **Testes unitários** (JUnit 5 + Mockito) cobrindo API HTTP, cálculo de taxas e filtros de moeda.

---

## 🛠️ Pré‐Requisitos

1. **Java 17+** (ou JDK 11+) instalado  
   ```bash
   java -version
````

2. **Maven 3.6+** (ou Gradle 6+)

   ```bash
   mvn -v
   ```
3. **Chave da ExchangeRate‐API**

   * Crie sua conta gratuita em [https://www.exchangerate-api.com/](https://www.exchangerate-api.com/)
   * Anote a sua `API_KEY`.

---

## 🎯 Passo a Passo de Setup

1. **Clone o repositório**

   ```bash
   git clone https://github.com/DelNeroDados/Test.git
   cd Test
   ```

2. **Crie o `.gitignore`** (já incluído no repo):

   ```gitignore
   /target/
   ```

/out/
/*.class
/.idea/
/*.iml
/logs/
/\*.log
favorites.txt
src/main/resources/application.properties

```

3. **Estrutura de pastas** (Maven padrão):
```

Test/
├── pom.xml
├── .gitignore
└── src/
├── main/
│   ├── java/com/delnero/conversormoeda/…
│   └── resources/application.properties
└── test/java/com/delnero/conversormoeda/…

````

4. **Externalize a chave**  
Crie **`src/main/resources/application.properties`**:
```properties
exchange.api.key=AD2405BE8DFB3FCA89729582
````

*Ou* defina variável de ambiente:

```bash
export EXCHANGE_API_KEY=AD2405BE8DFB3FCA89729582
```

5. **Compile e rode os testes**

   ```bash
   mvn clean install
   mvn test
   ```

6. **Execute a aplicação**

   * **Modo Console**

     ```bash
     mvn exec:java -Dexec.mainClass="com.delnero.conversormoeda.console.ConvertendoNoPrompt"
     ```

   * **Interface Gráfica (Swing)**

     ```bash
     mvn exec:java -Dexec.mainClass="com.delnero.conversormoeda.ui.CurrencyConverterUI"
     ```

---

## 📂 Organização de Pacotes

```text
com.delnero.conversormoeda
├── http/
│   └── ApiCliente.java           # cliente HTTP e desserialização JSON
├── model/
│   └── ConverterMoeda.java       # modelo de domínio das taxas
├── service/
│   ├── CalcularTaxas.java        # regra de negócio “valor × taxa”
│   ├── FiltroDeMoedas.java       # lógica de filtragem de moedas
│   ├── ConverterMoedaApi.java    # record para /latest/{base}
│   └── ConverterMoedaLivreApi.java # record para resultado manual
├── console/
│   └── ConvertendoNoPrompt.java  # modo texto com menu e favoritos
└── ui/
    └── CurrencyConverterUI.java  # interface Swing amigável
```

---

## 🔍 Testes Unitários

* **`ApiClienteTest`**: Mock HTTP com Mockito; testa sucesso 200 e falha 4xx/5xx.
* **`CalcularTaxasTest`**: Verifica resultado de `montante × taxa`.
* **`FiltroDeMoedasTest`**: Garante que nomes inválidos são filtrados.

```bash
mvn test
```

---

## 📝 Logging (SLF4J + Logback)

* **Dependências** em `pom.xml`:

  ```xml
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.9</version>
  </dependency>
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.4.11</version>
  </dependency>
  ```
* **Uso** em `ApiCliente.java`:

  ```java
  private static final Logger logger = LoggerFactory.getLogger(ApiCliente.class);
  logger.info("Buscando taxas para base={}", base);
  logger.error("Erro HTTP: {}", status);
  ```

---

## ✅ Multi-Catch

Exemplo em `ConvertendoNoPrompt`:

```java
try {
    api.buscaLivre(base, target, amount);
} catch (IOException | InterruptedException | CustomApiException e) {
    logger.error("Falha na conversão", e);
}
```

---

## 🎯 Separation of Concerns

* **`ApiCliente`**: comunicação HTTP.
* **`ConverterService`** / **`CalcularTaxas`**: cálculos.
* **`FavoriteManager`**: leitura/gravação de favoritos.
* **`UI`** e **`Console`**: interações distintas, sem misturar lógica de negócio.

---

## 🌟 Conclusão

Este projeto demonstra:

* **Engenharia de software**: arquitetura limpa, modular e testável.
* **Boas práticas**: gestão de dependências, externalização de config, logging, testes automatizados e `.gitignore` bem definido.
* **Qualidade de entrega**: GUI Swing profissional + modo CLI completo.
