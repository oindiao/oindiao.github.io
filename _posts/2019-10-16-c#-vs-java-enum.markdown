---
layout: post
title:  "C# vs Java: Enum"
date:   2019-10-16 06:00:00 -0300
categories: programação
---

> *"[...] pessoas de todo o mundo estão constantemente criando aplicações Web usando .NET, Java e PHP. Nenhuma delas está falhando por causa da escolha da tecnologia [...]"*
(Joel Spolsky. Language wars. 2006.)


A citação acima é para mim, de longe, a melhor definição de que **não existe melhor linguagem**. O que existe são diferenças entre elas e é sobre isso que irei escrever ao decorrer de diversos posts. Mas este será espcificamente sobre **ENUM**.

Enum
----

Enum é a representação de uma lista de valores constantes e pré definidos.

Um exemplo em Java para representar os dias da semana:

{% highlight java %} 
public enum DaysOfWeek {

    SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY;

}
{% endhighlight %}

Podemos acessar os valores de um enum direto, sem ter que instanciar a classe.

{% highlight java %}
System.out.println( DaysOfWeek.MONDAY );
{% endhighlight %}

A saída será:

{% highlight java %}
MONDAY
{% endhighlight %}

E como é uma lista, é possível pegar o valor da posição.

{% highlight java %}
System.out.println( DaysOfWeek.MONDAY.ordinal() );
{% endhighlight %}

A saída será:

{% highlight java %}
1 // A lista começa em 0, logo MONDAY será 1
{% endhighlight %}

Um Enum pode ser utilizado para diversas tarefas, principalmente quando queremos dar nomes mais amigáveis em valores pré-definidos de forma mais simples, como os dias da semana.

Enum facilita bastante a vida. Principalmente no Java, onde enum é um tipo de classe bem flexível e extensível. E pensei que a mesma facilidade eu iria encontrar no C#. Aí me enganei.

O que eu queria resolver
------------------------

Entrei em um projeto feito em C# e eu deveria criar duas funcionalidades:

1. Um método que teria como entrada o tipo de pedido e retornaria uma lista dos pedidos;
2. Um método que iria retornar a quantidade de pedido por tipo de pedido.

O que eu planejei pensando que era igual Java
---------------------------------------------

A partir de agora vou usar exemplos e o código será didático, sem usar teste unitário, mock de dados, tabela, etc. A intenção é de mostrar as dificuldades que tive e não de desenvolver um sistema.

Durante a *planning* rascunhei algo mais ou menos da seguinte maneira: crio uma interface onde irá retornar as listas de pedidios, crio um enum com os tipos e no construtor de cada tipo de pedido eu coloco uma descrição e qual classe irá retornar a lista de pedidos.

Meio confuso, mas a implementação é a seguinte:

A classe de pedido seria composta pelo requisitante, data de vencimento e o valor.

{% highlight java %}
public class Order {

    private String requester;
    private Date dueDate;
    private Double value;

    // ...
    // construtor, get, set, etc
    // ...

}
{% endhighlight %}

No meu contexto os pedidos poderiam vir de várias fontes de dados. Então para deixar isolado a forma de obter a lista de pedidos, criaria uma interface:

{% highlight java %}
public interface InterfaceOrderData {
    List<Order> get();
}
{% endhighlight %}

E, para cada tipo de pedido que houvesse, implementaria essa interface:

{% highlight java %}
public class Payment  implements InterfaceOrderData { /*implementação*/ }
public class Purchase implements InterfaceOrderData { /*implementação*/ }
public class Tax implements InterfaceOrderData { /*implementação*/ }
public class Travel implements InterfaceOrderData { /*implementação*/ }
{% endhighlight %}

Agora que vem a "mágica" do enum do java: posso colocar no construtor a interface *InterfaceOrderData* e em cada enum instanciar as classes que implementam a interface!

{% highlight java %}
public enum OrderType {

    TRAVEL ( "Viagem", new Travel() ), 
    
    // o enum TRAVEL instancia a classe Travel() pois
    // pois o construtor do enum recebe a interface InterfaceOrderData
    
    PURCHASE ( "Compras", new Purchase() ),
    PAYMENT ( "Pagamento", new Payment()),
    TAX ("Impostos", new Tax());

    // O mesmo é feito para os outros enum

    private final String description;
    private final InterfaceOrderData interfaceOrderData;

    OrderType(String description, InterfaceOrderData interfaceOrderData){
        this.description = description;

        this.interfaceOrderData = interfaceOrderData;
    }

    public String getDescription() {
        return description;
    }
    
    public InterfaceOrderData getInterfaceOrderData() {
        return interfaceOrderData;
    }
}
{% endhighlight %}

A mágica acima possibilita fazer a seguinte invocação:

{% highlight java %}
    List<Order> list = OrderType.TRAVEL.getInterfaceOrderData().get();
{% endhighlight %}

Era isto que eu estava em mente quando fiz a pontuação da funcionalidade.

Finalizando o exemplo, as duas funcionalidades serão representadas pela seguinte interface:

{% highlight java %}
public interface InterfaceOrderManager {
    List<Order> getListOrderByType(OrderTypeDto type);
    Map<OrderTypeDto, Integer> getSizeOfType();
}
{% endhighlight %}

O enum OrderTypeDto:

{% highlight java %}
public enum OrderTypeDto {

    TRAVEL ,
    PURCHASE ,
    PAYMENT ,
    TAX ;

    public static OrderType get(OrderTypeDto dto){
        return OrderType.valueOf(dto.name());
    }

}
{% endhighlight %}

Então a implementação do método *getListOrderByType* seria algo assim:

{% highlight java %}
public List<Order> getListOrderByType(OrderTypeDto type) {
    OrderType orderType = OrderTypeDto.get(type);

    return orderType.getInterfaceOrderData().get();
}
{% endhighlight %}

E do método *getSizeOfType* ficaria:

{% highlight java %}
public Map<OrderTypeDto, Integer> getSizeOfType() {

    HashMap<OrderTypeDto, Integer> map = new HashMap<OrderTypeDto, Integer>();

    for (OrderTypeDto dto: OrderTypeDto.values()){
        map.put(dto, getListOrderByType(dto).size());
    }

    return map;
}
{% endhighlight %}

Então se chamasse o método *getListOrderByType*, passando o tipo **PAYMENT**, poderia obter uma resposta do tipo:

{% highlight javascript %}
[
    {
        "requester":"Rafael", 
        "dueDate":"14/04/2019", 
        "value":150.0
    }, 
    {
        "requester":"Carlos", 
        "dueDate":"03/12/2019", 
        "value":300.6
    }
]
{% endhighlight %}

E o retorno do método teríamos:

{% highlight javascript %}
{
    "PAYMENT": 2, 
    "PURCHASE": 4, 
    "TAX": 2, 
    "TRAVEL": 3
}
{% endhighlight %}

Mas quando cheguei no C# para fazer, na intuição de usar a mesma técnica do enum... dei com os burros na água.

Caso queira ver todo o código acima, clique [aqui](https://github.com/rafael-rfp/java-enum "código no github"){:target="_blank"}

Enum no C#
----------

Como implementei no C#
----------------------

Conclusão
---------