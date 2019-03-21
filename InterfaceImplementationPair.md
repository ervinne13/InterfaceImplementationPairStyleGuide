# Interface-Implementation Pair Style Guide

### __Original Style__
I originally follow Martin Fowler's style for this with a bit of modification: 

__Martin Fowler Style__
```java
private Warehouse warehouse = new WarehouseImpl();
```

__My Style__
```java
private Warehouse warehouse = new WarehouseDefaultImpl();
```
Adding default just simply allows me to make a consistent distinction when I finally need to create different implementations. This __rarely__ happens though. What happens is that I have lots of classes that doesn't really need different implementations. 

__I always have doubts in using this approach. Is separating interface and implementation really necessary?__
 
Then I discovered that Martin Fowler has the same sentiments: [see here](https://martinfowler.com/bliki/InterfaceImplementationPair.html) 

### __My Revised Style__

The revised style's mantra is simply __"Don't create interfaces until you need to."__ specifically appliying to [Published Interfaces](https://martinfowler.com/bliki/PublishedInterface.html) (as described by Martin Fowler). But we can't ditch encapsulation or being able to use interfaces as well.

But we'll be needing some things first in order to achieve this, specifically, removing the need to use ``new`` in classes by making use of __factories__ and __providers__.

__Object Creation by Factories__
```java
private Customer customer = CustomerFactory:create();
```

```java
public class CustomerFactory {
    public static Customer create() {
        //  as the system evolves, here is where we add in different implementations or 
        //  creation logic which can depend on a configuration or whatever.
        return new Customer();
    }
}
```

__Why bother removing new and creating possibly unecessary factory classes?__ We seem to be increasing the complexity this way.

### The need to remove manually instantiating classes:

We introduce factories so that we may easily change the ```Customer``` class into an interface without breaking anything in the client code. Remember __"Don't create interfaces until you need to."__ but we also expect these classes to evolve into interfaces later on when they get multiple implementations. __You can also create an interface right away if you think it's necessary and it will remain consistent in the client classes__ regardless if they are using a class or an interface. They all need to get instances through a factory (or better, a __container__).

Remember that these are [Published Interfaces](https://martinfowler.com/bliki/PublishedInterface.html) (or Published Classes in this case), that means we want to avoid making the client classes manually create them with ```new```, otherwise, we'll have to update the client classes as well whenever we add implementations.

__Object Creation by Providers__
```java
//  private int id = <something>
private Customer customer = CustomerProvider:getById(id);

//  or by using repositories where:
//  private CustomerRepository customerRepo = <customer repository creation>
private Customer customer = customerRepo.getById(id);
```

__Abusing Your Framework's Container__

The best approach I can think of is to just use the framework's automated dependency injection (if it's available in whatever you're using). This way __we can ditch creating factories and providers entirely__ and still prevent the client classes from manually instantiating (service location) our service classes.

So something like below would work seemlessly

```java
public class PayrollProcessorController {
    // Here, we just let the framework inject things for us, no factories :D
    public function handleProcess(PayrollProcessorService $srvc) {
        // ....
    }
}
```

__Some Notes in Object Lookup__

I also like to avoid repositories as these grow very large overtime (if they do grow). So a distinction is a lot of times necessary, especially when dealing with integration with other systems.

We create a ```CustomerProvider``` class and a ```CustomerPersistence``` class for this.

__Example__

```java
//  The provider class grows on its own with the many ways you can lookup a resource
public interface CustomerProvider
{
    Customer getById(int id);
    Customer getByEmail(String email);
    List<Customer> getByIdList(int[] id);
}
```

```java
//  The persistence grows as creation and update logic can be large (especially when integrating with other systems)
public interface CustomerPersistence
{
    Customer create(Customer customer);
    Customer update(Customer customer);
    void delete(Customer customer);
    void deleteById(int id);
}
```

