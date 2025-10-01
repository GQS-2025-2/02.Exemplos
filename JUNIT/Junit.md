# 📘 Testes Unitários

## 🎯 O que é um teste unitário?
- Um **teste unitário** valida um método isoladamente, garantindo que ele funcione de acordo com as regras de negócio.
- Realiza teste individualizado nas **menores porções** possíveis de um sistema, ou seja, no código trabalhado pelo programador. 
- Não serve para testar o sistema globalmente, mas indica se os métodos, as classes ou as funções estão definidas no programa de maneira que recebam as entradas necessárias e produzam as saídas correspondentes aos requisitos da aplicação.
- Via de regra, tanto o planejamento quanto a execução do teste unitário ficam a cargo do próprio programador ou desenvolvedor do sistema. 
   - Durante a implementação do programa, o programador codifica a unidade e executa testes a cada progresso.
- É importante planejar os testes unitários.
- É importante que o testador conheça detalhadamente os requisitos da funcionalidade ou da unidade que será testada. 
- Ele deve compreender quais são as entradas das quais a unidade depende e quais são as saídas que ela deve produzir, bem como a maneira como os fluxos de controle agem no decorrer da execução.
- “Unidade” é o termo genérico utilizado para fazer referência à estrutura, à menor parte do sistema que será testada no teste unitário. 
- A unidade pode ser tratada também como **unit**.

**Padrão AAA** (Arrange–Act–Assert):
1. **Arrange:** preparar o cenário (instanciar objetos, dados de entrada).  
2. **Act:** executar a ação a ser testada.  
3. **Assert:** verificar saídas/efeitos (resultado, estado, exceções).

Boas práticas:
- Um **cenário por teste** (nomes descritivos).
- Testes **independentes e determinísticos**.
- Cobrir **casos de sucesso** e **de erro** (ex.: `assertThrows`).

# Junit 

## 📖 O que é o JUnit?
- O **JUnit** é o framework de testes unitários mais popular do ecossistema Java.  
- Ele é **open source** e serve como base para praticamente todas as ferramentas de automação e integração contínua.

### 🔎 História do JUnit
- **1997** → Criado por **Kent Beck** (um dos pais do *Extreme Programming* e do TDD) e **Erich Gamma** (autor do *Design Patterns*).  
- Inspirado na ideia de **xUnit**, uma família de frameworks de teste para diversas linguagens.  
- Tornou-se rapidamente padrão de mercado em projetos Java.

### 🕰️ Evolução das versões
- **JUnit 3 (anos 2000)**  
  - Primeira versão amplamente utilizada.  
  - Testes eram métodos que começavam com `test...` e precisavam herdar de `TestCase`.  
  - Exemplo:  
    ```java
    public class CalculadoraTest extends TestCase {
        public void testSomar() {
            assertEquals(5, new Calculadora().somar(2, 3));
        }
    }
    ```
- **JUnit 4 (2006)**  
  - Introduziu **anotações** (`@Test`, `@Before`, `@After`).  
  - Não exigia mais herança de `TestCase`.  
  - Simplificou muito a escrita dos testes.  
- **JUnit 5 (2017 em diante)**  
  - Arquitetura modular (**JUnit Platform, JUnit Jupiter e JUnit Vintage**).  
  - `JUnit Jupiter` é a API moderna (usa `org.junit.jupiter.api.*`).  
  - Suporte a **Lambda**, **Streams**, testes parametrizados, repetidos, tags, `@Nested`, `@DisplayName`, etc.  
  - É a versão **recomendada** para projetos atuais.  

Por que **JUnit 5 (Jupiter)**?
- Anotações novas: `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`, `@DisplayName`, `@Nested`, `@ParameterizedTest`, `@RepeatedTest`, `@Timeout`, `@Tag`.
- Injeções úteis (`@TempDir`, `TestInfo`, `TestReporter`), **Extension API**.
- Melhor integração com **Maven/Gradle/IDEs**.

## Contexto de Domínio - Biblioteca

No contexto de uma **biblioteca**, podemos ter regras como:
- Um livro deve estar disponível para ser emprestado.
- Um usuário não pode pegar mais de 3 livros ao mesmo tempo.
- A data de devolução não pode ser anterior à data de empréstimo.

---
## Estrutura Maven 

biblioteca-domain/
│── pom.xml                     # Arquivo de configuração Maven
│
├── src/
│   ├── main/                   # Código de produção
│   │   └── java/
│   │       └── com/
│   │           └── example/
│   │               └── biblioteca/
│   │                   ├── Livro.java
│   │                   ├── Usuario.java
│   │                   └── Emprestimo.java
│   │
│   └── resources/              # Arquivos de configuração (se houver)
│
└── src/
    └── test/                   # Código de testes unitários
        ├── java/
        │   └── com/
        │       └── example/
        │           └── biblioteca/
        │               ├── LivroTest.java
        │               ├── UsuarioTest.java
        │               └── EmprestimoTest.java
        │
        └── resources/          # Configurações específicas de teste (se houver)

## Código de Produção

**Pacote:** `com.example.biblioteca`

### `Livro.java`
```java
package com.example.biblioteca;

import java.util.Objects;

public class Livro {

    private final String titulo;
    private boolean disponivel = true;

    public Livro(String titulo) {
        if (titulo == null || titulo.isBlank()) {
            throw new IllegalArgumentException("Título do livro não pode ser vazio");
        }
        this.titulo = titulo;
    }

    public String getTitulo() { return titulo; }
    public boolean isDisponivel() { return disponivel; }

    void marcarIndisponivel() {
        if (!disponivel) throw new IllegalStateException("Livro já está emprestado");
        this.disponivel = false;
    }

    void marcarDisponivel() { this.disponivel = true; }

    @Override public String toString() {
        return "Livro{titulo='" + titulo + "', disponivel=" + disponivel + "}";
    }

    @Override public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Livro)) return false;
        Livro livro = (Livro) o;
        return titulo.equalsIgnoreCase(livro.titulo);
    }

    @Override public int hashCode() { return Objects.hash(titulo.toLowerCase()); }
}
```

### `Usuario.java`
```java
package com.example.biblioteca;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Objects;

public class Usuario {

    public static final int LIMITE_EMPRESTIMOS = 3;

    private final String nome;
    private final List<Emprestimo> emprestimosAtivos = new ArrayList<>();

    public Usuario(String nome) {
        if (nome == null || nome.isBlank()) {
            throw new IllegalArgumentException("Nome do usuário é obrigatório");
        }
        this.nome = nome;
    }

    public String getNome() { return nome; }
    public List<Emprestimo> getEmprestimosAtivos() { return List.copyOf(emprestimosAtivos); }

    public Emprestimo adicionarEmprestimo(Livro livro) {
        Objects.requireNonNull(livro, "Livro é obrigatório");
        if (emprestimosAtivos.size() >= LIMITE_EMPRESTIMOS) {
            throw new IllegalStateException("Usuário já possui " + LIMITE_EMPRESTIMOS + " empréstimos ativos");
        }
        Emprestimo e = new Emprestimo(this, livro);
        emprestimosAtivos.add(e);
        return e;
    }

    public void devolver(Livro livro) {
        Objects.requireNonNull(livro, "Livro é obrigatório");
        boolean devolvido = false;
        Iterator<Emprestimo> it = emprestimosAtivos.iterator();
        while (it.hasNext()) {
            Emprestimo e = it.next();
            if (e.getLivro().equals(livro) && e.isAtivo()) {
                e.devolver();
                it.remove();
                devolvido = true;
                break;
            }
        }
        if (!devolvido) {
            throw new IllegalStateException("Nenhum empréstimo ativo encontrado para este livro");
        }
    }
}
```

### `Emprestimo.java`
```java
package com.example.biblioteca;

import java.time.LocalDate;
import java.util.Objects;

public class Emprestimo {

    private final Usuario usuario;
    private final Livro livro;
    private final LocalDate dataEmprestimo;
    private LocalDate dataDevolucao;
    private boolean ativo = true;

    public Emprestimo(Usuario usuario, Livro livro) {
        this(usuario, livro, LocalDate.now());
    }

    public Emprestimo(Usuario usuario, Livro livro, LocalDate dataEmprestimo) {
        if (usuario == null || livro == null) {
            throw new IllegalArgumentException("Usuário e livro são obrigatórios");
        }
        if (!livro.isDisponivel()) {
            throw new IllegalStateException("Livro indisponível para empréstimo");
        }
        this.usuario = usuario;
        this.livro = livro;
        this.dataEmprestimo = Objects.requireNonNull(dataEmprestimo, "Data de empréstimo é obrigatória");
        livro.marcarIndisponivel();
    }

    public void devolver() {
        if (!ativo) return; 
        this.ativo = false;
        this.dataDevolucao = LocalDate.now();
        livro.marcarDisponivel();
    }

    public boolean isAtivo() { return ativo; }
    public Usuario getUsuario() { return usuario; }
    public Livro getLivro() { return livro; }
    public LocalDate getDataEmprestimo() { return dataEmprestimo; }
    public LocalDate getDataDevolucao() { return dataDevolucao; }
}
```

## Configuração do Maven (JUnit 5 + Surefire)

**`pom.xml` (mínimo para JUnit 5):**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>biblioteca</artifactId>
  <version>1.0.0</version>
  <properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <junit.jupiter.version>5.10.2</junit.jupiter.version>
  </properties>
  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>${junit.jupiter.version}</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.2.5</version>
        <configuration>
          <useModulePath>false</useModulePath>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Rodar: `mvn test`


## 🔹 Principais Anotações do JUnit 5

- **Anotações** controlam ciclo de vida, organização e repetição dos testes.
- Deve-se importar: `org.junit.jupiter.api.*`

### `@Test`
Marca um método como teste.

```java
@Test
void deveRegistrarEmprestimo() {
    Livro livro = new Livro("Clean Code");
    Usuario usuario = new Usuario("Rafaela");

    Emprestimo emprestimo = new Emprestimo(usuario, livro);

    assertEquals("Clean Code", emprestimo.getLivro().getTitulo());
}
```

---

### `@BeforeEach` e `@AfterEach`
Executam antes/depois de cada teste → útil para resetar estado.

```java
class EmprestimoTest {

    private Livro livro;
    private Usuario usuario;

    @BeforeEach
    void setup() {
        livro = new Livro("Refactoring");
        usuario = new Usuario("João");
    }

    @AfterEach
    void limpar() {
        livro = null;
        usuario = null;
    }

    @Test
    void deveCriarEmprestimoValido() {
        Emprestimo e = new Emprestimo(usuario, livro);
        assertNotNull(e);
    }
}
```

---

### `@BeforeAll` e `@AfterAll`
Executam uma única vez, antes/depois de todos os testes.

```java
@BeforeAll
static void conectarBaseDeDados() {
    System.out.println("Conectando ao BD de testes...");
}

@AfterAll
static void fecharBaseDeDados() {
    System.out.println("Fechando conexão...");
}
```

---

### `@Disabled`
Ignora temporariamente um teste.

```java
@Test
@Disabled("Em desenvolvimento: regra de multa por atraso")
void deveCalcularMultaPorAtraso() {
    // implementação futura
}
```

---

### `@DisplayName`
Dá nomes legíveis para relatórios.

```java
@Test
@DisplayName("Não deve permitir mais de 3 empréstimos por usuário")
void deveBloquearEmprestimoExtra() {
    Usuario usuario = new Usuario("Maria");
    for (int i = 1; i <= 3; i++) {
        usuario.adicionarEmprestimo(new Livro("Livro " + i));
    }
    assertThrows(IllegalStateException.class,
                 () -> usuario.adicionarEmprestimo(new Livro("Livro 4")));
}
```

---

### `@Nested`
Agrupa testes em blocos lógicos.

```java
class EmprestimoTest {

    @Nested
    class Criacao {
        @Test
        void deveRegistrarLivroDisponivel() {
            Livro livro = new Livro("DDD");
            Usuario usuario = new Usuario("Carlos");

            Emprestimo e = new Emprestimo(usuario, livro);
            assertFalse(livro.isDisponivel());
        }
    }

    @Nested
    class RegrasDevolucao {
        @Test
        void deveRegistrarDataDevolucaoValida() {
            Livro livro = new Livro("POO");
            Usuario usuario = new Usuario("Ana");
            Emprestimo e = new Emprestimo(usuario, livro);

            e.devolver();

            assertTrue(livro.isDisponivel());
        }
    }
}
```

---

### `@ParameterizedTest`
Testa vários cenários de uma vez.

```java
@ParameterizedTest
@ValueSource(strings = {"Clean Code", "Effective Java", "Refactoring"})
void tituloDeLivroNaoPodeSerVazio(String titulo) {
    Livro livro = new Livro(titulo);
    assertNotNull(livro.getTitulo());
}
```

---

### `@RepeatedTest`
Executa o mesmo teste várias vezes.

```java
@RepeatedTest(3)
void usuarioPodeEmprestarLivro() {
    Usuario usuario = new Usuario("Lucas");
    Livro livro = new Livro("Test-Driven Development");

    Emprestimo e = new Emprestimo(usuario, livro);

    assertEquals("Lucas", e.getUsuario().getNome());
}
```

---

## 🔹 Principais Asserções do JUnit 5 

- Cada método de teste deve ser avaliado em relação à condição verdadeira usando asserções para que o teste possa continuar a ser executado. 
- As asserções JUnit Jupiter são mantidas na classe org.junit.jupiter.api.Assertions
- **Asserções** verificam se o comportamento está correto.
### `assertEquals(expected, actual)`
Verifica igualdade.

```java
assertEquals("Clean Code", new Livro("Clean Code").getTitulo());
```

---

### `assertTrue` / `assertFalse`
Verifica condições booleanas.

```java
assertTrue(new Livro("Java").isDisponivel());
assertFalse(new Emprestimo(new Usuario("Ana"), new Livro("Java")).getLivro().isDisponivel());
```

---

### `assertThrows`
Valida exceções.

```java
Usuario usuario = new Usuario("Pedro");
for (int i = 0; i < 3; i++) {
    usuario.adicionarEmprestimo(new Livro("Livro " + i));
}
assertThrows(IllegalStateException.class,
             () -> usuario.adicionarEmprestimo(new Livro("Livro extra")));
```

---

### `assertAll`
Agrupa verificações.

```java
Livro livro = new Livro("Effective Java");

assertAll(
    () -> assertEquals("Effective Java", livro.getTitulo()),
    () -> assertTrue(livro.isDisponivel())
);
```

---

### `assertNotNull` / `assertNull`
Valida nullability.

```java
Livro livro = new Livro("POO");
assertNotNull(livro);
assertNull(null);
```

---

### `assertArrayEquals`
Compara arrays.

```java
String[] esperado = {"Clean Code", "Refactoring"};
String[] atual = {"Clean Code", "Refactoring"};
assertArrayEquals(esperado, atual);
```
## Código de Teste

### `LivroTest.java`
```java
package com.example.biblioteca;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

class LivroTest {

    @Test
    @DisplayName("Deve criar livro disponível com título válido")
    void criarLivroValido() {
        Livro l = new Livro("Clean Code");
        assertAll(
            () -> assertEquals("Clean Code", l.getTitulo()),
            () -> assertTrue(l.isDisponivel())
        );
    }

    @ParameterizedTest
    @ValueSource(strings = {"", " ", "   "})
    @DisplayName("Deve rejeitar títulos vazios ou em branco")
    void tituloInvalido(String titulo) {
        assertThrows(IllegalArgumentException.class, () -> new Livro(titulo));
    }

    @Test
    @DisplayName("Não deve permitir marcar indisponível duas vezes")
    void marcarIndisponivelDuasVezes() {
        Livro l = new Livro("Refactoring");
        l.marcarIndisponivel();
        assertThrows(IllegalStateException.class, l::marcarIndisponivel);
    }
}
```

### `EmprestimoTest.java`
```java
package com.example.biblioteca;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class EmprestimoTest {

    private Usuario usuario;
    private Livro livro;

    @BeforeEach
    void setup() {
        usuario = new Usuario("Rafaela");
        livro = new Livro("Domain-Driven Design");
    }

    @Test
    @DisplayName("Ao criar empréstimo, livro fica indisponível")
    void emprestimoMarcaLivroIndisponivel() {
        Emprestimo e = new Emprestimo(usuario, livro);
        assertAll(
            () -> assertSame(usuario, e.getUsuario()),
            () -> assertSame(livro, e.getLivro()),
            () -> assertFalse(livro.isDisponivel()),
            () -> assertTrue(e.isAtivo())
        );
    }

    @Test
    @DisplayName("Devolver empréstimo torna livro disponível")
    void devolverEmprestimo() {
        Emprestimo e = new Emprestimo(usuario, livro);
        e.devolver();
        assertAll(
            () -> assertFalse(e.isAtivo()),
            () -> assertNotNull(e.getDataDevolucao()),
            () -> assertTrue(livro.isDisponivel())
        );
    }

    @Test
    @DisplayName("Não deve emprestar livro já indisponível")
    void naoEmprestaLivroIndisponivel() {
        new Emprestimo(usuario, livro);
        assertThrows(IllegalStateException.class, () -> new Emprestimo(new Usuario("Maria"), livro));
    }
}
```

### `UsuarioTest.java`
```java
package com.example.biblioteca;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class UsuarioTest {

    private Usuario usuario;

    @BeforeEach
    void setup() {
        usuario = new Usuario("João");
    }

    @Test
    @DisplayName("Usuário começa sem empréstimos ativos")
    void semEmprestimosAtivosInicialmente() {
        assertTrue(usuario.getEmprestimosAtivos().isEmpty());
    }

    @Nested
    class LimiteEmprestimos {

        @Test
        @DisplayName("Pode emprestar até o limite")
        void podeEmprestarAteOLimite() {
            usuario.adicionarEmprestimo(new Livro("L1"));
            usuario.adicionarEmprestimo(new Livro("L2"));
            usuario.adicionarEmprestimo(new Livro("L3"));
            assertEquals(Usuario.LIMITE_EMPRESTIMOS, usuario.getEmprestimosAtivos().size());
        }

        @Test
        @DisplayName("Ultrapassar limite deve lançar exceção")
        void ultrapassarLimiteLancaExcecao() {
            usuario.adicionarEmprestimo(new Livro("L1"));
            usuario.adicionarEmprestimo(new Livro("L2"));
            usuario.adicionarEmprestimo(new Livro("L3"));
            assertThrows(IllegalStateException.class, () -> usuario.adicionarEmprestimo(new Livro("L4")));
        }
    }

    @Nested
    class Devolucao {

        @Test
        @DisplayName("Devolver remove empréstimo da lista ativa")
        void devolverRemoveDaListaAtiva() {
            Livro l = new Livro("Clean Architecture");
            usuario.adicionarEmprestimo(l);
            usuario.devolver(l);
            assertTrue(usuario.getEmprestimosAtivos().isEmpty());
            assertTrue(l.isDisponivel());
        }

        @Test
        @DisplayName("Devolver livro não emprestado deve falhar")
        void devolverLivroNaoEmprestadoFalha() {
            Livro l = new Livro("Effective Java");
            assertThrows(IllegalStateException.class, () -> usuario.devolver(l));
        }
    }
}
```

## Tabela de Exceções em Projetos Java
| Exceção                             | Quando usar                                                                                            | Exemplo no projeto Biblioteca                                   |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------- |
| **`IllegalArgumentException`**      | Quando um **parâmetro passado a um método é inválido** (null, vazio, fora do intervalo, etc.)          | Criar `new Livro("")` → título vazio não permitido              |
| **`IllegalStateException`**         | Quando a operação pedida **não faz sentido no estado atual** do objeto                                 | Tentar emprestar um livro já emprestado                         |
| **`NullPointerException`**          | Quando há tentativa de acessar algo em um objeto **nulo** (geralmente deve ser evitada com validações) | Se não verificássemos `usuario == null` ao criar `Emprestimo`   |
| **`IndexOutOfBoundsException`**     | Quando acessamos uma posição inválida em uma lista, array ou string                                    | Buscar `emprestimos.get(10)` em lista com menos de 10 elementos |
| **`UnsupportedOperationException`** | Quando um método é chamado mas **não é suportado** por aquela classe ou coleção                        | Chamar `remove()` numa lista imutável retornada por `List.of()` |
| **`IOException`**                   | Quando ocorre erro em operações de **entrada/saída** (arquivos, rede)                                  | Ao salvar relatório de empréstimos em arquivo                   |
| **`ArithmeticException`**           | Quando há erro aritmético (ex.: divisão por zero)                                                      | Se implementássemos média de páginas e divisor fosse zero       |
| **`Custom Exception`**              | Criadas pelo desenvolvedor para representar regras de negócio específicas                              | Ex.: `LimiteEmprestimosExcedidoException`                       |


## Principais ferramentas de **testes unitários** em outras linguagens.
---
## Python

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **pytest** | Sintaxe simples, fixtures poderosas, plugins abundantes | `pip install pytest` | `pytest` |
| **unittest** (padrão) | Incluído no Python, estilo xUnit | — | `python -m unittest` |
| **nose2** | Sucessor do nose, descoberta automática | `pip install nose2` | `nose2` |
| **hypothesis** | Testes baseados em propriedades (fuzz) | `pip install hypothesis` | `pytest` / `python -m pytest` |

**Exemplo (pytest):**
```python
# test_calc.py
def somar(a, b): return a + b

def test_somar():
    assert somar(2, 3) == 5
```
```bash
pytest -q
```

---

## JavaScript / TypeScript

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **Jest** | Tudo-em-um (runner, mocks, cobertura), ótimo para TS/React | `npm i -D jest` (+ `ts-jest` p/ TS) | `npx jest` |
| **Vitest** | Rápido (Vite), compatível com API do Jest | `npm i -D vitest` | `npx vitest` |
| **Mocha** (+ Chai) | Flexível, modular | `npm i -D mocha chai` | `npx mocha` |
| **Jasmine** | BDD puro, standalone | `npm i -D jasmine` | `npx jasmine` |
| **AVA** | Execução paralela isolada | `npm i -D ava` | `npx ava` |

**Exemplo (Jest):**
```ts
// sum.test.ts
export const sum = (a:number,b:number)=>a+b;
test("sum 2+3=5", ()=> { expect(sum(2,3)).toBe(5) })
```

---

## .NET / C#

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **xUnit.net** | Idiomático, amplamente adotado | `dotnet add package xunit` | `dotnet test` |
| **NUnit** | Atributos ricos, maduro | `dotnet add package NUnit` | `dotnet test` |
| **MSTest** | Oficial Microsoft | `dotnet add package MSTest.TestFramework` | `dotnet test` |

**Exemplo (xUnit):**
```csharp
using Xunit;
public class CalcTests {
  int Somar(int a,int b)=>a+b;
  [Fact] public void Soma_basica()=>Assert.Equal(5, Somar(2,3));
}
```

---

## Ruby

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **RSpec** | BDD expressivo, matchers ricos | `gem install rspec` | `rspec` |
| **Minitest** | Leve, padrão com Ruby | — | `ruby -Itest -e '...'` / `rake test` |

**Exemplo (RSpec):**
```ruby
# calc_spec.rb
def sum(a,b); a+b; end
RSpec.describe "sum" do
  it "2+3=5" do expect(sum(2,3)).to eq 5 end
end
```

---

## PHP

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **PHPUnit** | Padrão de mercado | `composer require --dev phpunit/phpunit` | `vendor/bin/phpunit` |
| **Pest** | Sintaxe minimalista inspirada em Jest | `composer require --dev pestphp/pest` | `./vendor/bin/pest` |

**Exemplo (PHPUnit):**
```php
// tests/CalcTest.php
use PHPUnit\Framework\TestCase;
final class CalcTest extends TestCase {
  public function testSum(){ $this->assertEquals(5, 2+3); }
}
```

---

## Go

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **testing** (padrão) | Embutido, simples | — | `go test ./...` |
| **testify** | Asserções e mocks | `go get github.com/stretchr/testify` | `go test` |
| **ginkgo/gomega** | BDD, DSL expressiva | `go install ...` | `ginkgo` / `go test` |

**Exemplo (testing):**
```go
// calc_test.go
func Sum(a,b int) int { return a+b }
func TestSum(t *testing.T) {
  if Sum(2,3)!=5 { t.Fatalf("queria 5") }
}
```

---

## Rust

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **test** (padrão, `cargo test`) | Integrado ao cargo, atributos `#[test]` | — | `cargo test` |
| **proptest** | Propriedades (teste gerativo) | `cargo add proptest --dev` | `cargo test` |

**Exemplo:**
```rust
#[test]
fn soma_basica(){ assert_eq!(2+3,5) }
```

---

## Kotlin / JVM

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **Kotest** | Matchers/Property test/BDD | Gradle/Maven dep. | `gradle test` |
| **kotlin.test** | Padrão, multiplataforma | Dep. Kotlin stdlib | `gradle test` |
| **Spek** | BDD para Kotlin | Gradle dep. | `gradle test` |

**Exemplo (Kotest):**
```kotlin
class CalcTest: StringSpec({
  "2+3=5" { (2+3) shouldBe 5 }
})
```

---

## Swift / iOS

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **XCTest** | Oficial Apple, integrado ao Xcode/SPM | — | Xcode / `swift test` |
| **Quick + Nimble** | BDD + matchers expressivos | SPM/CocoaPods | Xcode / `swift test` |

**Exemplo (XCTest):**
```swift
func testSum(){ XCTAssertEqual(2+3,5) }
```

---

## C / C++

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **GoogleTest (gtest)** | Padrão de fato em C++ | CMake/vcpkg | `ctest` |
| **Catch2** | Header-only, simples | `add_subdirectory` / pkg | `ctest` |
| **doctest** | Header-only, muito leve | incluir header | executável |
| **CppUTest** | Focado em embarcados/C | build lib | `make test` |

**Exemplo (Catch2):**
```cpp
TEST_CASE("sum"){
  REQUIRE(2+3==5);
}
```

---

## Elixir / Erlang

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **ExUnit** (Elixir) | Embutido, rápido, async | — | `mix test` |
| **EUnit** (Erlang) | Padrão Erlang | — | `rebar3 eunit` |

**Exemplo (Elixir):**
```elixir
test "sum" do
  assert 2+3 == 5
end
```

---

## Scala

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **ScalaTest** | Vários estilos (FunSuite, WordSpec) | sbt dep. | `sbt test` |
| **MUnit** | Leve e moderna | sbt dep. | `sbt test` |
| **specs2** | BDD expressivo | sbt dep. | `sbt test` |

---

## Dart / Flutter

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **test** | Oficial do Dart | `dart pub add --dev test` | `dart test` |
| **flutter_test** | Oficial p/ Flutter (widgets) | embutido | `flutter test` |

**Exemplo (Dart):**
```dart
import 'package:test/test.dart';
void main() {
  test('2+3=5', () => expect(2+3, equals(5)));
}
```

---

## R

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **testthat** | Padrão tidyverse, reporter amigável | `install.packages("testthat")` | `devtools::test()` |

**Exemplo:**
```r
test_that("sum", { expect_equal(2+3, 5) })
```

---

## Julia

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **Test** (padrão) | Embutido, macros `@test` | — | `julia --project -e 'using Pkg; Pkg.test()'` |

**Exemplo:**
```julia
using Test
@test 2+3 == 5
```

---

## Haskell

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **Hspec** | BDD em Haskell | cabal/stack | `stack test` / `cabal test` |
| **QuickCheck** | Propriedades gerativas | dep. | `stack test` |
| **tasty** | Runner extensível | dep. | `stack test` |

---

## Lua

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **busted** | BDD-style, assertions ricas | luarocks | `busted` |

---

## Perl

| Ferramenta | Destaques | Instalação | Executar |
|---|---|---|---|
| **Test::More** | Padrão de testes | CPAN/cpanm | `prove -l` |

---