# Aula Prática de TDD

**Prof. Marco Tulio Valente**

Nesta aula prática, iremos simular uma sessão de uso de TDD. Para isso, usaremos o mesmo exemplo do livro de TDD, do Kent Beck. Especificamente, iremos reproduzir o exemplo do Cap. 1 até o Cap. 11.

Você deverá seguirtodos os passos abaixo e sempre rodar também os testes, após compilar cada versão do código. Tente fazer o exercício no mindset de TDD.


Instruções:

* Primeiro, crie um repositório no GitHub

* Vá seguindo o roteiro. Para facilitar, os passos são itemizadaos da seguinte forma: (1): Vermelho, (2): Verde, (3): Refactor. Isto é, exatamente os mesmos estados de desenvolvimento baseado em TDD (mais detalhes nos slides).

* Nos passos marcados com COMMIT, dê um commit e um push. Isso será usado no momento da correção, para garantirmos que seguiu a sequência do roteiro, passo-a-passo.

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

* Faça um **COMMIT & PUSH **

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

* (1.1) Novo teste/feature: tornando `Dollar` imutável, i.e., uma operação como `times` retorna um novo objeto e não modifica o objecto corrente:

```java
public void testMultiplication() {
   Dollar five = new Dollar(5);
   Dollar product = five.times(2);
   assertEquals(10, product.amount);
   product = five.times(3);
   assertEquals(new Dollar(15), product);
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

* (1.3) Novo teste/feature: método `equals`, para testar se dois `Dollar` são iguais

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

* (3.1) Refactor: vamos criar uma superclasse `Money`; subir `amount` para ela (como `protected`) e também subir `equals` (com alguns ajustes no código). Ou seja, ambos vão ser deletados das subclasses `Dollar` e `Franc` e movidos para `Money`. Depois, não esqueça de fazer `Dollar` e `Franc` herdarem de `Money`:	

```java
class Money  {
    protected int amount;
   
    public boolean equals(Object object)  {
	Money money = (Money) object;
        return amount == money.amount;
}

		

* (3.2) Mais alguns testes, que vão passar:

public void testEquality() {
   assertTrue(new Dollar(5).equals(new Dollar(5)));
   assertFalse(new Dollar(5).equals(new Dollar(6)));
   assertTrue(new Franc(5).equals(new Franc(5)));
   assertFalse(new Franc(5).equals(new Franc(6)));
}


* Faça um **COMMIT & PUSH **

(1) Mais um teste/feature (último assert abaixo): comparando Franc com 
     Dollar

public void testEquality() {
	assertTrue(new Dollar(5).equals(new Dollar(5)));
	assertFalse(new Dollar(5).equals(new Dollar(6)));
	assertTrue(new Franc(5).equals(new Franc(5)));
	assertFalse(new Franc(5).equals(new Franc(6)));
	assertFalse(new Franc(5).equals(new Dollar(5)));
}


(2) equals verifica também se classes são iguais

class Money {
    ...
    public boolean equals(Object object) {
        Money money = (Money) object;
        return amount == money.amount
				&& getClass().equals(money.getClass());
	}
}				

(3) Refactoring: times agora retorna Money (era Dollar e Franc)

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

// (1) e (2) Novo teste/feature - método fábrica - Money.dollar
// Objetivo: começar a eliminar a classe Dollar

public void testMultiplication() {
	 Money five = Money.dollar(5);
	 assertEquals(Money.dollar(10), five.times(2));
	 assertEquals(Money.dollar(15), five.times(3));
}

// veja que tivemos que tornar Money abstrata, para
compilar (com o método times abstrato)

abstract class Money {
    ...
   static Dollar dollar(int amount)  {
       return new Dollar(amount);
	}
	
	abstract Money times(int multiplier);  
}
    
(1) e (2): Novo teste/feature - Money.franc (fábrica
de Francos). Para entender melhor, o objetivo é
sumir com as classes Dollar e Franc (que já não
estão mais sendo referenciadas nos testes)

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
	
(1) e (2): Novo teste/feature: currency() 	

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

(3.1): Refactor: adicionando um atributo currency, em Franc e Dollar

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

(3.2) Refactoring: "pull up" currency para Money

abstract class Money {
    ...
    protected String currency;
    ....
    String currency() {
		return currency;
	}
} 

class Franc extends Money {	
	Franc(int amount) {
        this.amount = amount;
	    currency = "CHF";
	}
	....
}

class Dollar extends Money {
	 Dollar(int amount)  {
         this.amount = amount;
		currency = "USD";
	}
	...
}

(3.3.) Refactor: "times" agora retorna Money, tanto
em Dollar, como em Franc

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

(3.4) Refactor: construtores ganham um parâmetro "currency"			
	
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
     private String currency;
	
	Franc(int amount, String currency) {
         this.amount = amount;
		this.currency = currency;
	}
}

class Dollar extends Money {
     private String currency;
	
	 Dollar(int amount, String currency)  {
         this.amount = amount;
		this.currency = currency;
	}
}

(3.5) Pull Up construtores

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

(1) e (2) Novo teste/feature: comparando Money e Franc

public void testDifferentClassEquality() {
	assertTrue(new Money(10, "CHF").equals(new Franc(10, "CHF")));
}

Veja que a implementação envolve:

- tornar Money uma classe concreta (remover "abstract"):
- modificar o equals (com isso, moedas de classes diferente
podem ser iguais, basta que "amount" e "currency" sejam iguais.
- pull up "times", com alguns ajustes, para Money

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
         return amount == money.amount
			&& currency().equals(money.currency());
    }
	
	Money times(int multiplier) {
         return new Money(amount * multiplier, currency);
	}
}

(3) Refactor: 

- remover classesDollar e Franc 

- alterar os seguinte teste, conforme abaixo:

public void testEquality() {
	 assertTrue(Money.dollar(5).equals(Money.dollar(5)));
	 assertFalse(Money.dollar(5).equals(Money.dollar(6)));
	 assertFalse(Money.franc(5).equals(Money.dollar(5)));
}	

- Remover: 
testDifferentClassEquality()

- Remover:
testFrancMultiplication() 

