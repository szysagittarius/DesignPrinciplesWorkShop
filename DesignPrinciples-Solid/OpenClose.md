
## Open Close Principle 
- Closed For Modification 
    - New features getting added to the software component, should NOT have to modify existing code.
- Open For Extension 
    - A software component should be extendable to add a new feature or to add a new behavior to it.


```
Code Example:
One State Insurance - For all your health insurance needs!

public class InsurancePremiumDiscountCalculator {
    public int calculatePremiumDiscountPercent(HealthInsuranceCustomerProfile customer) {
        if (customer.isLoyalCustomer()) {
            return 20;
        }
        return 0;
    }
}

public class HealthInsuranceCustomerProfile {
    public boolean isLoyalCustomer() {
        return true; //or false
    }
}

```
> **Warning:**
> Code always should be ready to change. A wave of changes are incoming... !

```
One State Insurance - For all your health and vehicle insurance needs!

public class InsurancePremiumDiscountCalculator {
    public int calculatePremiumDiscountPercent(HealthInsuranceCustomerProfile customer) {
        if (customer.isLoyalCustomer()) {
            return 20;
        }
        return 0;
    }
}

public class HealthInsuranceCustomerProfile {
    public boolean isLoyalCustomer() {
        return true; //or false
    }
}

public class VehicleInsuranceCustomerProfile {
    public boolean isLoyalCustomer() {
        return true; //or false
    }
}

```
So, are we done? No, this is where the problems begin.
- We now have to modify the Calculator class, because the calculate method currently takes in a healthInsuranceCustomerProfile object.
- We want it to take in a VehicleInsuranceCustomerProfile object as well. 

The only way out is to add a new overloaded method which takes in a VehicleInsuranceCustomerProfile object. 

Code will become like this - we modify 2 classes in order to implment our logic.

```
public class InsurancePremiumDiscountCalculator {
    public int calculatePremiumDiscountPercent(HealthInsuranceCustomerProfile customer) {
        if (customer.isLoyalCustomer()) {
            return 20;
        }
        return 0;
    }

    public int calculatePremiumDiscountPercent(VehicleInsuranceCustomerProfile customer) {
        if (customer.isLoyalCustomer()) {
            return 20;
        }
        return 0;
    }
}

public class HealthInsuranceCustomerProfile {
    public boolean isLoyalCustomer() {
        return true; //or false
    }
}

public class VehicleInsuranceCustomerProfile {
    public boolean isLoyalCustomer() {
        return true; //or false
    }
}
```

This is just the beginning. 
- What if we want to handle home insurance too? 
We will need to add code again to this Calculator class. So, why is this not good
- Because in order to add a new feature, we are having to touch existing code,
which goes against our Open Closed Principle. The existing code is supposed to be closed for modification.


> **Tip:**
> Lets refactor our design and see if we can solve this problem.

```
public class InsurancePremiumDiscountCalculator {
    public int calculatePremiumDiscountPercent(CustomerProfile customer) {
        if (customer.isLoyalCustomer()) {
            return 20;
        }
        return 0;
    }
}

public interface CustomerProfile {
    public boolean isLoyalCustomer();
}

public class HealthInsuranceCustomerProfile implements CustomerProfile {
    @Override
    public boolean isLoyalCustomer() {
        return true; // or false
    }
}

public class VehicleInsuranceCustomerProfile implements CustomerProfile {
    @Override
    public boolean isLoyalCustomer() {
        return true; // or false
    }
}

public class HomeInsuranceCustomerProfile implements CustomerProfile {
    @Override
    public boolean isLoyalCustomer() {
        return true; // or false
    }
}

```


We will revert our Calculator class back. 
We will create a new interface named CustomerProfile.
- The interface defines only one method: isLoyalCustomer
- We will make both our customerprofile classes implement this common interface.

Now in the Calculator class, we'll change the method argument.
- Instead of a HealthInsuranceCustomerProfile, we will make it CustomerProfile.
- We are done.

> **Tip:**
> On the face of it, it might look like we had to do more work this time. 
> But the beauty of this design lies in how it handles future extensions. 
> - Assume, One State enters home insurance business as well. They change their tagline again. So we will need to create a HomeInsuranceCustomerProfile object.
> - We make it implement the common CustomerProfile interface.
> - And then? What else do we need to do? Nothing, we do NOT need to touch the Calculator class at all.


All we did is : add a new class by implementing an existing interface.
Neither the calculator class, nor the interface nor any of the existing classes had to be modified.
- See how this makes the process of adding extensions much cleaner.

What we saw now was an example of the Open Closed Principle. When we started out with this example, it was NOT following the open closed principle. So we picked holes in the design.



## SOLID Principles Reflection

- SOLID principles are all intertwined and interdependent.
- SOLID principles are most effective when they are combined together.
- It is important to get a wholesome view of all the SOLID principles.



## A word of caution

1. Do not follow the open Closed Principle blindly.
   - You will end up with a huge number of classes that can complicate your overall design.
   - e.g. if you were to fix a bug, and if you think existing code has to be modified in order to fix the bug effectively, then go do it.
    
2. You will end up with a huge number of classes that can complicate your overall design.
   - But if you see repeated occurences of certain kinds of bugs, and you think that revamping your design can help reduce it, then by all means, do it.

3. Make a subjective, rather than an objective decision.