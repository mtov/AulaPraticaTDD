# Aula Prática de TDD

**Prof. Marco Tulio Valente**

Objetivo: reproduzir uma sessão de desenvolvimento de software usando TDD (Test-Driven Development).

Para isso, usaremos o mesmo exemplo do livro clássico de TDD, do Kent Beck. Especificamente, iremos reproduzir o exemplo do Cap. 1 até o Cap. 11 do livro (os capítulos são pequenos, com 3-4 páginas).

Faça o exercício com o modelo mental de TDD. Ou seja, para tirar proveito do exercício, é importante não apenas seguir o roteiro mecanicamente, mas também pensar sempre sobre as mudanças de fluxo de trabalho que ocorrem com TDD, os benefícios e desafios dessa técnica de escrita de código e testes.

Se necessário, estude antes o material visto em sala de aula. Caso tenha dúvidas, você pode consultar também o nosso [livro](https://engsoftmoderna.info), que explica os passos com mais detalhes.

O exemplo está em Java, mas a sintaxe é familiar mesmo para aqueles que nunca programaram na linguagem. Infelizmente, não é possível fazer em uma outra linguagem, pois a correção será automática. Você pode usar uma IDE Java (Eclipse, NetBeans, IntelliJ) ou então uma IDE online (como repl.it). Além disso, a próxima aula prática também será em Java.

Instruções:

* Primeiro, crie um repositório no GitHub.

* Vá seguindo o roteiro, passo a passo. Para facilitar, os passos são itemizadaos da seguinte forma: (1): Vermelho, (2): Verde, (3): Refactor. Isto é, exatamente os mesmos estados de desenvolvimento baseado em TDD.

* Após cada passo, compile e rode os testes. 

* Nos passos marcados com **COMMIT & PUSH**, dê um commit e um push no seu repositório. Isso será usado no momento da correção, para garantir que seguiu a sequência do roteiro, passo-a-passo.

* **Importante**: quando terminar, responda à seguinte [issue](https://github.com/mtov/AulaPraticaTDD/issues/9), informando a URL do seu repositório e seu nome (**caso contrário, seu trabalho não será corrigido**).


# Roteiro


* (1) Seja a seguinte classe e seu teste (exatamente o teste e a classe vistos em sala de aula, no final dos slides de TDD):

```java
    class Dollar {
       int amount = 10;
       Dollar(int amount) {}			
       void times(int multiplier) {}
    }	

    public void testMultiplication() {
       Dollar five = new Dollar(5);
       five.times(2);
       assertEquals(10, five.amount);
    }
```

* Faça um **COMMIT & PUSH**

* (2) Dando uma implementação mais real para a classe `Dollar`:

```java
class Dollar {
   int amount;
   Dollar(int amount) {
      this.amount= amount;
   }
   void times(int multiplier) {
      amount= amount * multiplier;
   }
}	
```

* (1.1) Novo teste/feature: tornando `Dollar` imutável, i.e., agora, uma operação como `times` retorna um novo objeto e não modifica o objecto corrente:

```java
public void testMultiplication() {
   Dollar five = new Dollar(5);
   Dollar product = five.times(2);
   assertEquals(10, product.amount);
   product = five.times(3);
   assertEquals(new Dollar(15), product);
}
```
Precisamos alterar `Dolar.times()` para compilar o novo teste:

```java
class Dollar {
   ...
   Dollar times(int multiplier) {
      return new Dollar(amount * multiplier);
   }
}
```

* (1.2) Apenas um refactoring (no teste), para ele ficar menor:

```java
public void testMultiplication() {
   Dollar five = new Dollar(5);
   assertEquals(new Dollar(10), five.times(2));
   assertEquals(new Dollar(15), five.times(3));
}
```

* (1.2.1) `Dollar` é a única classe usando a variável `amount` após as mudanças no teste. Logo, podemos alterar o seu modificador de acesso para `private`:

```java
class Dollar {
   private int amount;
   ...
}
```

* (1.3) Novo teste/feature: método `equals`, para testar se dois `Dollar` são iguais.

```java
public void testEquality() {
   assertTrue(new Dollar(5).equals(new Dollar(5)));
   assertFalse(new Dollar(5).equals(new Dollar(6)));
}
```

* (2.1) Implementando `equals` (método da classe `Dollar`):

```java
public boolean equals(Object object)  {
   Dollar dollar = (Dollar) object;
   return amount == dollar.amount;
}
```

* (1) e (2): Novo teste/feature: classe `Franc` (além de dólar, agora temos uma classe para armazenar francos suíços); `Franc` é basicamente uma cópia de Dollar, com os devidos ajustes de nomes.

```java
public void testFrancMultiplication() {
   Franc five = new Franc(5);
   assertEquals(new Franc(10), five.times(2));
   assertEquals(new Franc(15), five.times(3));
}

class Franc {   
   private int amount;					
   Franc(int amount) {      
      this.amount= amount;
    }					
    Franc times(int multiplier)  {      
       return new Franc(amount * multiplier);					
    }   
    public boolean equals(Object object) {					
       Franc franc = (Franc) object;      
       return amount == franc.amount;					
     }					
}
```

* (3.1) Refactor: vamos criar uma superclasse `Money`; subir `amount` para ela (como `protected`) e também subir `equals` (com alguns ajustes no código). Ou seja, ambos vão ser deletados das subclasses `Dollar` e `Franc` e movidos para `Money`. Depois, não se esqueça de fazer `Dollar` e `Franc` herdarem de `Money`.	

```java
class Money  {
   protected int amount;
   
   public boolean equals(Object object)  {
      Money money = (Money) object;
      return amount == money.amount;
   }   
}
```
		
* (3.2) Mais alguns testes, que vão passar:

```java
public void testEquality() {
   assertTrue(new Dollar(5).equals(new Dollar(5)));
   assertFalse(new Dollar(5).equals(new Dollar(6)));
   assertTrue(new Franc(5).equals(new Franc(5)));
   assertFalse(new Franc(5).equals(new Franc(6)));
}
```

* Faça um **COMMIT & PUSH**

* (1) Melhorando `testEquality` (só adicionando último `assert`, no código abaixo), que compara `Franc` com `Dollar`.

```java
public void testEquality() {
   assertTrue(new Dollar(5).equals(new Dollar(5)));
   assertFalse(new Dollar(5).equals(new Dollar(6)));
   assertTrue(new Franc(5).equals(new Franc(5)));
   assertFalse(new Franc(5).equals(new Franc(6)));
   assertFalse(new Franc(5).equals(new Dollar(5)));
}
```

* (2) Agora `equals` verifica se as classes dos objetos comparados são iguais.

```java
class Money {
   ...
   public boolean equals(Object object) {
      Money money = (Money) object;
      return amount == money.amount && getClass().equals(money.getClass());
   }
}				
```

* (3) Refactoring: `times` agora retorna `Money` (era `Dollar` e `Franc`). Basicamente, estamos preparando o terreno para daqui a pouco remover essas classes. O código delas é muito parecido; e o objetivo do passo de Refactor em TDD é principalmente remover duplicação.

```java
class Dollar {
   ...
   Money times(int multiplier)  {
      return new Dollar(amount * multiplier);
   }								
}    

class Franc {
   ...
   Money times(int multiplier)  {
      return new Franc(amount * multiplier);
   }								
}    
```

* (1) e (2) Novo teste/feature - método fábrica, chamado `Money.dollar`; mais uma passo para começar a eliminar a classe `Dollar`.

```java
public void testMultiplication() {
   Money five = Money.dollar(5);
   assertEquals(Money.dollar(10), five.times(2));
   assertEquals(Money.dollar(15), five.times(3));
}
```

Adicionalmente, veja que tivemos que tornar `Money` abstrata, para compilar (com o método `times` abstrato):

```java
abstract class Money {
   ...
   static Dollar dollar(int amount)  {
      return new Dollar(amount);
   }
	
   abstract Money times(int multiplier);  
}
```
    
* (1) e (2): Novo teste/feature - `Money.franc` (fábrica de Francos). 

```java
public void testEquality() {
   assertTrue(Money.dollar(5).equals(Money.dollar(5)));
   assertFalse(Money.dollar(5).equals(Money.dollar(6)));
   assertTrue(Money.franc(5).equals(Money.franc(5)));
   assertFalse(Money.franc(5).equals(Money.franc(6)));
   assertFalse(Money.franc(5).equals(Money.dollar(5)));
}

public void testFrancMultiplication() {
   Money five = Money.franc(5);
   assertEquals(Money.franc(10), five.times(2));
   assertEquals(Money.franc(15), five.times(3));
}

class Money {
   ...
   static Money dollar(int amount)  {
      return new Dollar(amount);
   }
   
   static Money franc(int amount) {
      return new Franc(amount);
    }
} 
```

* Faça um **COMMIT & PUSH**

* (1) e (2): Novo teste/feature: `currency()`. 	

```java
public void testCurrency() {
   assertEquals("USD", Money.dollar(1).currency());
   assertEquals("CHF", Money.franc(1).currency());
}

abstract class Money {
   ...
   abstract String currency();
} 

class Franc extends Money {
   ...
   String currency() {
      return "CHF";
   }
} 

class Dollar extends Money {
    ...
    String currency() {
       return "USD";
    }
} 
```

* (3.1): Refactor: adicionando um atributo `currency`, em `Franc` e `Dollar`.

```java
class Franc extends Money {
   private String currency;
	
   Franc(int amount) {
      this.amount = amount;
      currency = "CHF";
   }
   
   String currency() {
      return currency;
   }
}

class Dollar extends Money {
   private String currency;
	
   Dollar(int amount) {
      this.amount = amount;
      currency = "USD";
   }
   
   String currency() {
      return currency;
   }
}
```

* (3.2) Refactoring: subir `currency` para `Money` (como `protected`).

```java
abstract class Money {
   ...
   protected String currency;
   ...
   String currency() {
      return currency;
   }
} 

class Franc extends Money {	
   Franc(int amount) {
      this.amount = amount;
      currency = "CHF"; 
   }
   ...
}

class Dollar extends Money {
   Dollar(int amount)  {
      this.amount = amount;
      currency = "USD";
   }
   ...
}
```

* (3.3.) Refactor: `times` agora retorna `Money`, tanto em `Dollar`, como em `Franc`.

```java
class Dollar {
   ...
   Money times(int multiplier)  {
      return Money.dollar(amount * multiplier);
   }								
}    

class Franc {
   ...
   Money times(int multiplier)  {
      return Money.franc(amount * multiplier);
   }								
}    
```

* (3.4) Refactor: construtores ganham um parâmetro `currency`.			

```java
abstract class Money {
   ...
   static Money dollar(int amount)  {
      return new Dollar(amount, "USD");
   }
   
   static Money franc(int amount) {
      return new Franc(amount, "CHF");
   }
} 

class Franc extends Money {
	
   Franc(int amount, String currency) {
      this.amount = amount;
      this.currency = currency;
   }
}

class Dollar extends Money {
	
   Dollar(int amount, String currency)  {
      this.amount = amount;
      this.currency = currency;
   }
}
```

* (3.5) Subir construtores para `Money`:

```java
abstract class Money {
   private String currency; 

   static Money dollar(int amount)  {
      return new Dollar(amount, "USD");
   }

   static Money franc(int amount) {
      return new Franc(amount, "CHF");
   }

   Money(int amount, String currency) {
      this.amount = amount;
      this.currency = currency;
   }
}

class Franc extends Money {	
   Franc(int amount, String currency) {
      super(amount, currency);
   }
     
   Money times(int multiplier)  {
      return Money.franc(amount * multiplier);
   }
}

class Dollar extends Money {	
   Dollar(int amount, String currency)  {
      super(amount, currency);
   }
	
   Money times(int multiplier)  {
      return Money.dollar(amount * multiplier);
   }
}
```

* Faça um **COMMIT & PUSH**

* (1) e (2) Novo teste/feature: comparando `Money` e `Franc`.

```java
public void testDifferentClassEquality() {
   assertTrue(new Money(10, "CHF").equals(new Franc(10, "CHF")));
}
```

A implementação inclui ainda: tornar `Money` uma classe concreta (remover `abstract`);
modificar o `equals` (com isso, moedas de classes diferente
podem ser iguais, basta que `amount` e `currency` sejam iguais;
subir `times`, com alguns ajustes, para `Money`.

```java
class Money {
   ...
   static Money dollar(int amount)  {
      return new Dollar(amount, "USD");
   }
	
   static Money franc(int amount) {
      return new Franc(amount, "CHF");
   }
    
   Money(int amount, String currency) {
      this.amount = amount;
      this.currency = currency;
   }
	
   public boolean equals(Object object) {
      Money money = (Money) object;
      return amount == money.amount && currency().equals(money.currency());
   }
	
   Money times(int multiplier) {
      return new Money(amount * multiplier, currency);
   }
}
```

* (3) Refactor: Alterar o seguinte teste:

```java
public void testEquality() {
   assertTrue(Money.dollar(5).equals(Money.dollar(5))); 
   assertFalse(Money.dollar(5).equals(Money.dollar(6)));
   assertFalse(Money.franc(5).equals(Money.dollar(5)));
}	
```

Remover classes `Dollar` e `Franc`. Alterar as referências na classe `Money`:

```java
class Money {
   static Money dollar(int amount)  {
      return new Money(amount, "USD");
   }

   static Money franc(int amount) {
      return new Money(amount, "CHF");
   }
   ...
}
```



E remover  `testDifferentClassEquality()` e `testFrancMultiplication()`.

* Faça um **COMMIT & PUSH**

