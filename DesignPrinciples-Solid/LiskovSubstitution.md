## Liskov Substitution Principle 

Liskov Substitution Principle (LSP):

    Definition: Objects of a superclass should be replaceable with objects of a subclass without altering the correctness of the program.
    Emphasis: Subtypes must be substitutable for their base types.

"Is-A" Relationship:

    A common way of thinking about inheritance is that a subclass should be a specialized version of its superclass.
    For example, a Desert Eagle and an AK-47 can both be considered as specific types of Gun, so they satisfy the "is-a" relationship.
    However, a Toy Gun may not satisfy the requirements of a true gun in certain contexts, and its substitutability may break the LSP.


Example: Guns and Toy Guns
- We can create a class hierarchy involving a base class Gun and two types of actual guns (AK-47 and Desert Eagle). We will also include a ToyGun to demonstrate a violation of the LSP.

```csharp
public class Gun
{
    public virtual void Shoot()
    {
        Console.WriteLine("Firing bullets!");
    }
}

//The Gun class has a Shoot method that prints a message indicating that bullets are fired. This is common behavior for all guns.

public class AK47 : Gun
{
    public override void Shoot()
    {
        Console.WriteLine("AK-47 firing automatic rounds!");
    }
}

public class DesertEagle : Gun
{
    public override void Shoot()
    {
        Console.WriteLine("Desert Eagle firing high-caliber rounds!");
    }
}

//Both the AK47 and DesertEagle inherit from Gun, and they override the Shoot method to specify their unique firing mechanisms. These satisfy the "is-a" relationship, and can be substituted wherever a Gun is expected without any issues.

public class ToyGun : Gun
{
    public override void Shoot()
    {
        Console.WriteLine("Click! No bullets here, just a toy.");
    }
}

//The ToyGun also inherits from Gun and overrides the Shoot method, but instead of firing bullets, it simply clicks. While the toy gun technically satisfies the "is-a" relationship, it violates the Liskov Substitution Principle because a ToyGun cannot function in the same way as a Gun.
```
### Demonstrating the Violation of LSP:
Guns
![alt text](Images/Ak47.png)
![alt text](Images/DesertEagle.png)
![alt text](Images/WaterGun.png)
```csharp
public void FireAllGuns(Gun gun)
{
    gun.Shoot();
}

//If you pass an instance of AK47 or DesertEagle to the FireAllGuns method, it will work as expected:

Gun ak47 = new AK47();
FireAllGuns(ak47);  // Output: "AK-47 firing automatic rounds!"

Gun desertEagle = new DesertEagle();
FireAllGuns(desertEagle);  // Output: "Desert Eagle firing high-caliber rounds!"

//However, if you pass a ToyGun:
Gun toyGun = new ToyGun();
FireAllGuns(toyGun);  // Output: "Click! No bullets here, just a toy."

//This behavior may violate the assumptions about the behavior of a Gun, thus breaking the Liskov Substitution Principle.
```
## two patterns of Liskov Substitution violation and solution

### Pattern 1
Example #1 
Car and Racing Car
![alt text](Images/RacingCar.png)
![alt text](Images/Car.png)
```csharp
public class Car {
    public double getCabinWidth() {
        // return cabin width
    }
}

public class RacingCar extends Car {
    @Override
    public double getCabinWidth() {
        // UNIMPLEMENTED!
    }

    public double getCockpitWidth() {
        // return cockpit width
    }
}

public class CarUtils {
    public static void main(String[] args) {
        Car first = new Car();
        Car second = new Car();
        Car third = new RacingCar();

        List<Car> myCars = new ArrayList<>();
        myCars.add(first);
        myCars.add(second);
        myCars.add(third);

        for (Car car : myCars) {
            System.out.println(car.getCabinWidth());
        }
    }
}
```

We have to strike at the root, which is the inheritance itself. 

So we have to break the inheritance. 
    - RacingCar should no longer extend Car. Instead we'll come up with a common parent for both.
    - So we create a new class called Vehicle which is a very generic class, which can represent any modes of transportation including a truck, a boat, or an airplane. Then we make both Car and RacingCar extend this Vehicle class.

We need to restructure the code also accordingly.
    - First two iterations, the getInteriorWidth() method gets called on the car objects. The Car objects, in turn,call  their getCabinWidth method.
    - In the third and last iteration, the getInteriorWidth() method gets called on the RacingCar objects, which in turn, call their getCockpitWidth method. 

So all calls work correctly. And the whole thing is dynamic.
    - Because of the way we designed the hierarchy, we can simply iterate through any number of objects of reference type Vehicle and get the interior width ,without ever bothering to know if they are Car objects or  RacingCar objects.

### Refactored code
adjust inheritance
```
public class Vehicle {
    public double getInteriorWidth() {
        // return interior width
    }
}

public class Car extends Vehicle {
    @Override
    public double getInteriorWidth() {
        return this.getCabinWidth();
    }

    public double getCabinWidth() {
        // return cabin width
    }
}

public class RacingCar extends Vehicle {
    @Override
    public double getInteriorWidth() {
        return this.getCockpitWidth();
    }

    public double getCockpitWidth() {
        // return cockpit width
    }
}

public class VehicleUtils {
    public static void main(String[] args) {
        Vehicle first = new Car();
        Vehicle second = new Car();
        Vehicle third = new RacingCar();

        List<Vehicle> myVehicles = new ArrayList<>();
        myVehicles.add(first);
        myVehicles.add(second);
        myVehicles.add(third);

        for (Vehicle vehicle : myVehicles) {
            System.out.println(vehicle.getInteriorWidth());
        }
    }
}
```


### Pattern 2

```
public class Product {
    protected double discount;

    public double getDiscount() {
        return discount;
    }
}

public class InHouseProduct extends Product {
    public void applyExtraDiscount() {
        discount = discount * 1.5;
    }
}

public class PricingUtils {
    public static void main(String[] args) {
        Product p1 = new Product();
        Product p2 = new Product();
        Product p3 = new InHouseProduct();

        List<Product> productList = new ArrayList<>();
        productList.add(p1);
        productList.add(p2);
        productList.add(p3);

        for (Product product : productList) {
            if (product instanceof InHouseProduct) {
                ((InHouseProduct) product).applyExtraDiscount();
            }
            System.out.println(product.getDiscount());
        }
    }
}
```

### Refactored code
Wrap up in subclass
```
public class Product {
    protected double discount = 20;

    public double getDiscount() {
        return discount;
    }
}

public class InHouseProduct extends Product {
    @Override
    public double getDiscount() {
        this.applyExtraDiscount();
        return discount;
    }

    public void applyExtraDiscount() {
        discount = discount * 1.5;
    }
}

public class PricingUtils {
    public static void main(String[] args) {
        Product p1 = new Product();
        Product p2 = new Product();
        Product p3 = new InHouseProduct();

        List<Product> productList = new ArrayList<>();
        productList.add(p1);
        productList.add(p2);
        productList.add(p3);

        for (Product product : productList) {
            System.out.println(product.getDiscount());
        }
    }
}

```