# Git em Equipe (Java) — README

Mini–projeto para treinar **Git em equipe (3 pessoas)** com Java. 
Cada pessoa implementa uma parte da classe `Calc` em **branches** diferentes, abre **Pull Requests** e resolve **um conflito de merge** intencional no `README.md`.

---

## 🎯 Objetivos de aprendizado
- Clonar repositório, criar branch, commit, push e abrir **PR**  
- Fazer **code review** e realizar **merge**   
- **Sincronizar** sua branch com `main` e **resolver conflitos** 

---

## ✅ Pré-requisitos
- Git instalado  
- Java 17+ (ideal 21)  
- Maven instalado (ou use sua IDE preferida)  
- Um repositório GitHub com **acesso de escrita** para as 3 pessoas

---

## 🧱 Estrutura do projeto (crie assim no repo)
```
git-equipe-java/
├─ README.md
├─ pom.xml
└─ src
   └─ main
      └─ java
         └─ com
            └─ exemplo
               └─ equipe
                  ├─ Calc.java
                  └─ Main.java
```

---

## 🧩 Código base (cole nos arquivos correspondentes)

**README.md** (este arquivo — mantenha a linha abaixo para gerar conflito)  
```
Linha da equipe (edite ESTA MESMA LINHA para criar conflito): Equipe = [PREENCHER AQUI]
```

**pom.xml**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.exemplo</groupId>
  <artifactId>git-equipe-java</artifactId>
  <version>1.0.0</version>

  <properties>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.11.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

**src/main/java/com/exemplo/equipe/Calc.java**
```java
package com.exemplo.equipe;

public class Calc {
    // Pessoa A
    public int soma(int a, int b) {
        // TODO: implementar
        return 0; 
    }

    // Pessoa B
    public int subtracao(int a, int b) {
        // TODO: implementar
        return 0; 
    }

    // Pessoa C
    public int multiplicacao(int a, int b) {
        // TODO: implementar
        return 0; 
    }
}
```

**src/main/java/com/exemplo/equipe/Main.java**
```java
package com.exemplo.equipe;

public class Main {
    public static void main(String[] args) {
        Calc c = new Calc();
        System.out.println("2 + 3 = " + c.soma(2, 3));          // esperado: 5
        System.out.println("3 - 2 = " + c.subtracao(3, 2));     // esperado: 1
        System.out.println("2 * 3 = " + c.multiplicacao(2, 3)); // esperado: 6
    }
}
```

> Suba o esqueleto **com os métodos “errados”** (retornando 0). Cada pessoa corrige a sua parte.

---

## 🔀 Fluxo de trabalho recomendado
- Uma branch por tarefa: `feature/soma`, `feature/subtracao`, `feature/multiplicacao`  
- Commits pequenos e mensagens claras (`feat: implementar soma`)  
- Abrir **PR** e solicitar **review** de pelo menos 1 colega  
- **Todos editam a MESMA LINHA** do `README.md` (acima) para forçar **conflito**

---

## 👤 Configuração de identidade (cada um, uma única vez)
```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu.email@exemplo.com"
```

---

## 👩‍💻👨‍💻 Passo a passo por pessoa

> Substitua `<sua-org>`/`<seu-usuario>` conforme sua hospedagem.

### Pessoa A — Setup + **soma**
1. Crie o repositório `git-equipe-java` e adicione B e C como *collaborators*.  
2. Suba o código base.
3. Crie a branch:
   ```bash
   git checkout -b feature/soma
   ```
4. Implemente a soma em `Calc.java`:
   ```java
   public int soma(int a, int b) { return a + b; }
   ```
5. Edite **a linha do README** (a mesma de todos), ex.:
   ```
   Linha da equipe (edite ESTA MESMA LINHA para criar conflito): Equipe = [Ana]
   ```
6. Teste e envie:
   ```bash
   mvn -q -DskipTests package
   java -cp target/git-equipe-java-1.0.0.jar com.exemplo.equipe.Main

   git add .
   git commit -m "feat: implementar soma e atualizar equipe"
   git push -u origin feature/soma
   ```
7. Abra o **PR** `feature/soma` → `main`, peça review de B e C e faça **merge**.  
8. Atualize sua `main`:
   ```bash
   git checkout main
   git pull origin main
   ```

### Pessoa B — **subtração**
1. Crie a branch:
   ```bash
   git checkout -b feature/subtracao
   ```
2. Implemente a subtração:
   ```java
   public int subtracao(int a, int b) { return a - b; }
   ```
3. Edite **a mesma linha** do README:
   ```
   ... Equipe = [Bruno]
   ```
4. Traga a `main` para sua branch (se A já mesclou):
   ```bash
   git fetch origin
   git pull --rebase origin main
   ```
   > Se surgir conflito, resolva (veja seção de conflitos).
5. Teste, commit e push; abra **PR** e faça **merge** após review:
   ```bash
   mvn -q -DskipTests package
   java -cp target/git-equipe-java-1.0.0.jar com.exemplo.equipe.Main

   git add .
   git commit -m "feat: implementar subtracao e atualizar equipe"
   git push -u origin feature/subtracao
   ```

### Pessoa C — **multiplicação** + conflito
1. Crie a branch:
   ```bash
   git checkout -b feature/multiplicacao
   ```
2. Implemente a multiplicação:
   ```java
   public int multiplicacao(int a, int b) { return a * b; }
   ```
3. Edite **a mesma linha** do README:
   ```
   ... Equipe = [Carla]
   ```
4. Puxe a `main` (deve gerar conflito se PRs anteriores já foram mesclados):
   ```bash
   git fetch origin
   git pull --rebase origin main
   ```
5. **Resolva o conflito** e conclua o rebase (instruções abaixo), depois:
   ```bash
   mvn -q -DskipTests package
   java -cp target/git-equipe-java-1.0.0.jar com.exemplo.equipe.Main

   git push -u origin feature/multiplicacao
   ```
6. Abra **PR**, peça review e faça **merge**.

---

## ⚔️ Como resolver o conflito (README.md)
Ao conflitar, o arquivo mostrará algo como:
```diff
<<<<<<< HEAD
Linha da equipe (edite ESTA MESMA LINHA para criar conflito): Equipe = [Ana]
=======
Linha da equipe (edite ESTA MESMA LINHA para criar conflito): Equipe = [Bruno]
>>>>>>> feature/subtracao
```

Edite para uma versão combinada:
```markdown
Linha da equipe (edite ESTA MESMA LINHA para criar conflito): Equipe = [Ana, Bruno, Carla]
```

Depois:
```bash
git add README.md
# se estiver em rebase:
git rebase --continue
# (se estivesse em merge normal: git commit)
```

---

## 🧪 Como rodar
```bash
mvn -q -DskipTests package
java -cp target/git-equipe-java-1.0.0.jar com.exemplo.equipe.Main
```

Saída esperada ao final (com tudo implementado):
```
2 + 3 = 5
3 - 2 = 1
2 * 3 = 6
```

---

## 📝 Dicas de Git úteis
- Status: `git status`  
- Histórico: `git log --oneline --graph --decorate --all`  
- Trazer `main` para sua branch (rebase):
  ```bash
  git fetch origin
  git pull --rebase origin main
  ```
- Desfazer último commit mantendo alterações: `git reset --soft HEAD~1`  
- Guardar alterações temporariamente: `git stash` / `git stash pop`
