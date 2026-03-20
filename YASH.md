sup super smart girl
// 1. Define a source interface for the data
interface Order {
  id: number;
  userId: string;
  total: number;
  isActive: boolean;
}

// 2. Define a complex mapped type
// 'K in keyof T': Iterate over all keys K of a generic type T.
// 'as `get${Capitalize<string & K>}`': Remap the key K to a new name,
//    e.g., 'id' becomes 'getId'. This uses template literal types and the intrinsic Capitalize utility.
// ': () => T[K]': The value type for the new key is a function that returns
//    the type of the original property T[K].
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

// 3. Apply the type to create the Getters for the Order interface
type OrderGetters = Getters<Order>;

// 4. Implement a function to create an instance of the Getters
const createOrderGetters = (order: Order): OrderGetters => {
  const getters: Partial<OrderGetters> = {};
  for (const key in order) {
    if (Object.prototype.hasOwnProperty.call(order, key)) {
      // Use 'as keyof Order' to ensure type safety in the loop
      const prop = key as keyof Order;
      // Construct the getter method name using template literals
      const getterName = `get${
        prop.charAt(0).toUpperCase() + prop.slice(1)
      }` as keyof OrderGetters;
      
      // Assign the function, safely accessing the original property
      (getters[getterName] as () => Order[typeof prop]) = () => order[prop];
    }
  }
  return getters as OrderGetters;
};

// 5. Usage Example
const myOrder: Order = {
  id: 123,
  userId: "user-abc",
  total: 50.75,
  isActive: true,
};

const orderGetters = createOrderGetters(myOrder);

console.log(orderGetters.getId());       // Output: 123
console.log(orderGetters.getUserId());   // Output: user-abc
console.log(orderGetters.getTotal());    // Output: 50.75
console.log(orderGetters.getIsActive()); // Output: true

