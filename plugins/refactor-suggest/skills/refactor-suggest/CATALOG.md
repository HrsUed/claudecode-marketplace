# Martin Fowler リファクタリング カタログ

**出典**: [Refactoring 2nd Edition - refactoring.com](https://refactoring.com/catalog/)

---

## 目次

1. [基本的なリファクタリング](#1-基本的なリファクタリング)
2. [カプセル化](#2-カプセル化)
3. [特性の移動](#3-特性の移動)
4. [データの再編成](#4-データの再編成)
5. [条件記述の単純化](#5-条件記述の単純化)
6. [APIのリファクタリング](#6-apiのリファクタリング)
7. [継承の取り扱い](#7-継承の取り扱い)

---

## 1. 基本的なリファクタリング

### Extract Function
**別名**: Extract Method
**逆操作**: Inline Function

```javascript
// Before
function printOwing(invoice) {
  printBanner();
  let outstanding = calculateOutstanding();
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
}

// After
function printOwing(invoice) {
  printBanner();
  let outstanding = calculateOutstanding();
  printDetails(outstanding);

  function printDetails(outstanding) {
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${outstanding}`);
  }
}
```

---

### Inline Function
**別名**: Inline Method
**逆操作**: Extract Function

```javascript
// Before
function getRating(driver) {
  return moreThanFiveLateDeliveries(driver) ? 2 : 1;
}

function moreThanFiveLateDeliveries(driver) {
  return driver.numberOfLateDeliveries > 5;
}

// After
function getRating(driver) {
  return (driver.numberOfLateDeliveries > 5) ? 2 : 1;
}
```

---

### Extract Variable
**別名**: Introduce Explaining Variable
**逆操作**: Inline Variable

```javascript
// Before
return order.quantity * order.itemPrice -
  Math.max(0, order.quantity - 500) * order.itemPrice * 0.05 +
  Math.min(order.quantity * order.itemPrice * 0.1, 100);

// After
const basePrice = order.quantity * order.itemPrice;
const quantityDiscount = Math.max(0, order.quantity - 500) * order.itemPrice * 0.05;
const shipping = Math.min(basePrice * 0.1, 100);
return basePrice - quantityDiscount + shipping;
```

---

### Inline Variable
**別名**: Inline Temp
**逆操作**: Extract Variable

```javascript
// Before
let basePrice = anOrder.basePrice;
return (basePrice > 1000);

// After
return anOrder.basePrice > 1000;
```

---

### Change Function Declaration
**別名**: Rename Function, Rename Method, Add Parameter, Remove Parameter, Change Signature

```javascript
// Before
function circum(radius) {...}

// After
function circumference(radius) {...}
```

---

### Rename Variable

```javascript
// Before
let a = height * width;

// After
let area = height * width;
```

---

### Introduce Parameter Object

```javascript
// Before
function amountInvoiced(startDate, endDate) {...}
function amountReceived(startDate, endDate) {...}
function amountOverdue(startDate, endDate) {...}

// After
function amountInvoiced(aDateRange) {...}
function amountReceived(aDateRange) {...}
function amountOverdue(aDateRange) {...}
```

---

### Combine Functions into Class

```javascript
// Before
function base(aReading) {...}
function taxableCharge(aReading) {...}
function calculateBaseCharge(aReading) {...}

// After
class Reading {
  base() {...}
  taxableCharge() {...}
  calculateBaseCharge() {...}
}
```

---

### Combine Functions into Transform

```javascript
// Before
function base(aReading) {...}
function taxableCharge(aReading) {...}

// After
function enrichReading(argReading) {
  const aReading = _.cloneDeep(argReading);
  aReading.baseCharge = base(aReading);
  aReading.taxableCharge = taxableCharge(aReading);
  return aReading;
}
```

---

### Split Phase

```javascript
// Before
const orderData = orderString.split(/\s+/);
const productPrice = priceList[orderData[0].split("-")[1]];
const orderPrice = parseInt(orderData[1]) * productPrice;

// After
const orderRecord = parseOrder(order);
const orderPrice = price(orderRecord, priceList);

function parseOrder(aString) {
  const values = aString.split(/\s+/);
  return ({
    productID: values[0].split("-")[1],
    quantity: parseInt(values[1]),
  });
}

function price(order, priceList) {
  return order.quantity * priceList[order.productID];
}
```

---

## 2. カプセル化

### Encapsulate Record
**別名**: Replace Record with Data Class

```javascript
// Before
organization = {name: "Acme Gooseberries", country: "GB"};

// After
class Organization {
  constructor(data) {
    this._name = data.name;
    this._country = data.country;
  }
  get name()    {return this._name;}
  set name(arg) {this._name = arg;}
  get country()    {return this._country;}
  set country(arg) {this._country = arg;}
}
```

---

### Encapsulate Collection

```javascript
// Before
class Person {
  get courses() { return this._courses; }
  set courses(aList) { this._courses = aList; }
}

// After
class Person {
  get courses() { return this._courses.slice(); }
  addCourse(aCourse) { ... }
  removeCourse(aCourse) { ... }
}
```

---

### Encapsulate Variable
**別名**: Encapsulate Field, Self-Encapsulate Field

```javascript
// Before
let defaultOwner = {firstName: "Martin", lastName: "Fowler"};

// After
let defaultOwnerData = {firstName: "Martin", lastName: "Fowler"};
export function defaultOwner()       {return defaultOwnerData;}
export function setDefaultOwner(arg) {defaultOwnerData = arg;}
```

---

### Replace Primitive with Object
**別名**: Replace Data Value with Object, Replace Type Code with Class

```javascript
// Before
orders.filter(o => "high" === o.priority
                || "rush" === o.priority);

// After
orders.filter(o => o.priority.higherThan(new Priority("normal")))
```

---

### Replace Temp with Query

```javascript
// Before
const basePrice = this._quantity * this._itemPrice;
if (basePrice > 1000)
  return basePrice * 0.95;
else
  return basePrice * 0.98;

// After
get basePrice() {
  return this._quantity * this._itemPrice;
}

if (this.basePrice > 1000)
  return this.basePrice * 0.95;
else
  return this.basePrice * 0.98;
```

---

### Extract Class
**逆操作**: Inline Class

```javascript
// Before
class Person {
  get officeAreaCode() {return this._officeAreaCode;}
  get officeNumber()   {return this._officeNumber;}
}

// After
class Person {
  get officeAreaCode() {return this._telephoneNumber.areaCode;}
  get officeNumber()   {return this._telephoneNumber.number;}
}

class TelephoneNumber {
  get areaCode() {return this._areaCode;}
  get number()   {return this._number;}
}
```

---

### Inline Class
**逆操作**: Extract Class

```javascript
// Before
class Person {
  get officeAreaCode() {return this._telephoneNumber.areaCode;}
  get officeNumber()   {return this._telephoneNumber.number;}
}
class TelephoneNumber {
  get areaCode() {return this._areaCode;}
  get number()   {return this._number;}
}

// After
class Person {
  get officeAreaCode() {return this._officeAreaCode;}
  get officeNumber()   {return this._officeNumber;}
}
```

---

### Hide Delegate
**逆操作**: Remove Middle Man

```javascript
// Before
manager = aPerson.department.manager;

// After
manager = aPerson.manager;

class Person {
  get manager() {
    return this.department.manager;
  }
}
```

---

### Remove Middle Man
**逆操作**: Hide Delegate

```javascript
// Before
manager = aPerson.manager;

class Person {
  get manager() {return this.department.manager;}
}

// After
manager = aPerson.department.manager;
```

---

## 3. 特性の移動

### Move Function
**別名**: Move Method

```javascript
// Before
class Account {
  get overdraftCharge() {...}
}

// After
class AccountType {
  get overdraftCharge() {...}
}
```

---

### Move Field

```javascript
// Before
class Customer {
  get plan() {return this._plan;}
  get discountRate() {return this._discountRate;}
}

// After
class Customer {
  get plan() {return this._plan;}
  get discountRate() {return this.plan.discountRate;}
}
```

---

### Move Statements into Function
**逆操作**: Move Statements to Callers

```javascript
// Before
result.push(`<p>title: ${person.photo.title}</p>`);
result.concat(photoData(person.photo));

function photoData(aPhoto) {
  return [
    `<p>location: ${aPhoto.location}</p>`,
    `<p>date: ${aPhoto.date.toDateString()}</p>`,
  ];
}

// After
result.concat(photoData(person.photo));

function photoData(aPhoto) {
  return [
    `<p>title: ${aPhoto.title}</p>`,
    `<p>location: ${aPhoto.location}</p>`,
    `<p>date: ${aPhoto.date.toDateString()}</p>`,
  ];
}
```

---

### Move Statements to Callers
**逆操作**: Move Statements into Function

```javascript
// Before
emitPhotoData(outStream, person.photo);

function emitPhotoData(outStream, photo) {
  outStream.write(`<p>title: ${photo.title}</p>\n`);
  outStream.write(`<p>location: ${photo.location}</p>\n`);
}

// After
emitPhotoData(outStream, person.photo);
outStream.write(`<p>location: ${person.photo.location}</p>\n`);

function emitPhotoData(outStream, photo) {
  outStream.write(`<p>title: ${photo.title}</p>\n`);
}
```

---

### Replace Inline Code with Function Call

```javascript
// Before
let appliesToMass = false;
for(const s of states) {
  if (s === "MA") appliesToMass = true;
}

// After
appliesToMass = states.includes("MA");
```

---

### Slide Statements
**別名**: Consolidate Duplicate Conditional Fragments

```javascript
// Before
const pricingPlan = retrievePricingPlan();
const order = retreiveOrder();
let charge;
const chargePerUnit = pricingPlan.unit;

// After
const pricingPlan = retrievePricingPlan();
const chargePerUnit = pricingPlan.unit;
const order = retreiveOrder();
let charge;
```

---

### Split Loop

```javascript
// Before
let averageAge = 0;
let totalSalary = 0;
for (const p of people) {
  averageAge += p.age;
  totalSalary += p.salary;
}
averageAge = averageAge / people.length;

// After
let totalSalary = 0;
for (const p of people) {
  totalSalary += p.salary;
}

let averageAge = 0;
for (const p of people) {
  averageAge += p.age;
}
averageAge = averageAge / people.length;
```

---

### Replace Loop with Pipeline

```javascript
// Before
const names = [];
for (const i of input) {
  if (i.job === "programmer")
    names.push(i.name);
}

// After
const names = input
  .filter(i => i.job === "programmer")
  .map(i => i.name);
```

---

### Remove Dead Code

```javascript
// Before
if(false) {
  doSomethingThatUsedToMatter();
}

// After
// (コード削除)
```

---

## 4. データの再編成

### Split Variable
**別名**: Remove Assignments to Parameters, Split Temp

```javascript
// Before
let temp = 2 * (height + width);
console.log(temp);
temp = height * width;
console.log(temp);

// After
const perimeter = 2 * (height + width);
console.log(perimeter);
const area = height * width;
console.log(area);
```

---

### Rename Field

```javascript
// Before
class Organization {
  get name() {...}
}

// After
class Organization {
  get title() {...}
}
```

---

### Replace Derived Variable with Query

```javascript
// Before
get discountedTotal() {return this._discountedTotal;}
set discount(aNumber) {
  const old = this._discount;
  this._discount = aNumber;
  this._discountedTotal += old - aNumber;
}

// After
get discountedTotal() {return this._baseTotal - this._discount;}
set discount(aNumber) {this._discount = aNumber;}
```

---

### Change Reference to Value

```javascript
// Before
class Product {
  applyDiscount(arg) {
    this._price.amount -= arg;
  }
}

// After
class Product {
  applyDiscount(arg) {
    this._price = new Money(
      this._price.amount - arg,
      this._price.currency
    );
  }
}
```

---

### Change Value to Reference

```javascript
// Before
let customer = new Customer(customerData);

// After
let customer = customerRepository.get(customerData.id);
```

---

## 5. 条件記述の単純化

### Decompose Conditional

```javascript
// Before
if (!aDate.isBefore(plan.summerStart) && !aDate.isAfter(plan.summerEnd))
  charge = quantity * plan.summerRate;
else
  charge = quantity * plan.regularRate + plan.regularServiceCharge;

// After
if (summer())
  charge = summerCharge();
else
  charge = regularCharge();
```

---

### Consolidate Conditional Expression

```javascript
// Before
if (anEmployee.seniority < 2) return 0;
if (anEmployee.monthsDisabled > 12) return 0;
if (anEmployee.isPartTime) return 0;

// After
if (isNotEligibleForDisability()) return 0;

function isNotEligibleForDisability() {
  return ((anEmployee.seniority < 2)
          || (anEmployee.monthsDisabled > 12)
          || (anEmployee.isPartTime));
}
```

---

### Replace Nested Conditional with Guard Clauses

```javascript
// Before
function getPayAmount() {
  let result;
  if (isDead)
    result = deadAmount();
  else {
    if (isSeparated)
      result = separatedAmount();
    else {
      if (isRetired)
        result = retiredAmount();
      else
        result = normalPayAmount();
    }
  }
  return result;
}

// After
function getPayAmount() {
  if (isDead) return deadAmount();
  if (isSeparated) return separatedAmount();
  if (isRetired) return retiredAmount();
  return normalPayAmount();
}
```

---

### Replace Conditional with Polymorphism

```javascript
// Before
switch (bird.type) {
  case 'EuropeanSwallow':
    return "average";
  case 'AfricanSwallow':
    return (bird.numberOfCoconuts > 2) ? "tired" : "average";
  case 'NorwegianBlueParrot':
    return (bird.voltage > 100) ? "scorched" : "beautiful";
  default:
    return "unknown";
}

// After
class EuropeanSwallow {
  get plumage() { return "average"; }
}
class AfricanSwallow {
  get plumage() { return (this.numberOfCoconuts > 2) ? "tired" : "average"; }
}
class NorwegianBlueParrot {
  get plumage() { return (this.voltage > 100) ? "scorched" : "beautiful"; }
}
```

---

### Introduce Special Case
**別名**: Introduce Null Object

```javascript
// Before
if (aCustomer === "unknown") customerName = "occupant";

// After
class UnknownCustomer {
    get name() {return "occupant";}
}
```

---

### Introduce Assertion

```javascript
// Before
if (this.discountRate)
  base = base - (this.discountRate * base);

// After
assert(this.discountRate >= 0);
if (this.discountRate)
  base = base - (this.discountRate * base);
```

---

### Replace Control Flag with Break
**別名**: Remove Control Flag

```javascript
// Before
for (const p of people) {
  if (! found) {
    if ( p === "Don") {
      sendAlert();
      found = true;
    }
  }
}

// After
for (const p of people) {
  if ( p === "Don") {
    sendAlert();
    break;
  }
}
```

---

## 6. APIのリファクタリング

### Separate Query from Modifier

```javascript
// Before
function getTotalOutstandingAndSendBill() {
  const result = customer.invoices.reduce(
    (total, each) => each.amount + total, 0
  );
  sendBill();
  return result;
}

// After
function totalOutstanding() {
  return customer.invoices.reduce(
    (total, each) => each.amount + total, 0
  );
}
function sendBill() {
  emailGateway.send(formatBill(customer));
}
```

---

### Parameterize Function
**別名**: Parameterize Method

```javascript
// Before
function tenPercentRaise(aPerson) {
  aPerson.salary = aPerson.salary.multiply(1.1);
}
function fivePercentRaise(aPerson) {
  aPerson.salary = aPerson.salary.multiply(1.05);
}

// After
function raise(aPerson, factor) {
  aPerson.salary = aPerson.salary.multiply(1 + factor);
}
```

---

### Remove Flag Argument
**別名**: Replace Parameter with Explicit Methods

```javascript
// Before
function setDimension(name, value) {
  if (name === "height") {
    this._height = value;
    return;
  }
  if (name === "width") {
    this._width = value;
    return;
  }
}

// After
function setHeight(value) {this._height = value;}
function setWidth(value) {this._width = value;}
```

---

### Preserve Whole Object

```javascript
// Before
const low = aRoom.daysTempRange.low;
const high = aRoom.daysTempRange.high;
if (aPlan.withinRange(low, high))

// After
if (aPlan.withinRange(aRoom.daysTempRange))
```

---

### Replace Parameter with Query
**別名**: Replace Parameter with Method
**逆操作**: Replace Query with Parameter

```javascript
// Before
availableVacation(anEmployee, anEmployee.grade);

function availableVacation(anEmployee, grade) {
  // calculate vacation...
}

// After
availableVacation(anEmployee)

function availableVacation(anEmployee) {
  const grade = anEmployee.grade;
  // calculate vacation...
}
```

---

### Replace Query with Parameter
**逆操作**: Replace Parameter with Query

```javascript
// Before
function targetTemperature(aPlan) {
  currentTemperature = thermostat.currentTemperature;
  // rest of function...
}

// After
function targetTemperature(aPlan, currentTemperature) {
  // rest of function...
}
```

---

### Remove Setting Method

```javascript
// Before
class Person {
  get name() {...}
  set name(aString) {...}
}

// After
class Person {
  get name() {...}
}
```

---

### Replace Constructor with Factory Function
**別名**: Replace Constructor with Factory Method

```javascript
// Before
leadEngineer = new Employee(document.leadEngineer, 'E');

// After
leadEngineer = createEngineer(document.leadEngineer);
```

---

### Replace Function with Command
**別名**: Replace Method with Method Object
**逆操作**: Replace Command with Function

```javascript
// Before
function score(candidate, medicalExam, scoringGuide) {
  let result = 0;
  let healthLevel = 0;
  // long body code
}

// After
class Scorer {
  constructor(candidate, medicalExam, scoringGuide) {
    this._candidate = candidate;
    this._medicalExam = medicalExam;
    this._scoringGuide = scoringGuide;
  }

  execute() {
    this._result = 0;
    this._healthLevel = 0;
    // long body code
  }
}
```

---

### Replace Command with Function
**逆操作**: Replace Function with Command

```javascript
// Before
class ChargeCalculator {
  constructor (customer, usage){
    this._customer = customer;
    this._usage = usage;
  }
  execute() {
    return this._customer.rate * this._usage;
  }
}

// After
function charge(customer, usage) {
  return customer.rate * usage;
}
```

---

### Return Modified Value

```javascript
// Before
let totalAscent = 0;
calculateAscent();

function calculateAscent() {
  for (let i = 1; i < points.length; i++) {
    const verticalChange = points[i].elevation - points[i-1].elevation;
    totalAscent += (verticalChange > 0) ? verticalChange : 0;
  }
}

// After
const totalAscent = calculateAscent();

function calculateAscent() {
  let result = 0;
  for (let i = 1; i < points.length; i++) {
    const verticalChange = points[i].elevation - points[i-1].elevation;
    result += (verticalChange > 0) ? verticalChange : 0;
  }
  return result;
}
```

---

### Replace Error Code with Exception

```javascript
// Before
if (data)
  return new ShippingRules(data);
else
  return -23;

// After
if (data)
  return new ShippingRules(data);
else
  throw new OrderProcessingError(-23);
```

---

### Replace Exception with Precheck
**別名**: Replace Exception with Test

```java
// Before
double getValueForPeriod (int periodNumber) {
  try {
    return values[periodNumber];
  } catch (ArrayIndexOutOfBoundsException e) {
    return 0;
  }
}

// After
double getValueForPeriod (int periodNumber) {
  return (periodNumber >= values.length) ? 0 : values[periodNumber];
}
```

---

## 7. 継承の取り扱い

### Pull Up Method
**逆操作**: Push Down Method

```javascript
// Before
class Employee {...}

class Salesman extends Employee {
  get name() {...}
}

class Engineer extends Employee {
  get name() {...}
}

// After
class Employee {
  get name() {...}
}

class Salesman extends Employee {...}
class Engineer extends Employee {...}
```

---

### Pull Up Field
**逆操作**: Push Down Field

```java
// Before
class Employee {...}

class Salesman extends Employee {
  private String name;
}

class Engineer extends Employee {
  private String name;
}

// After
class Employee {
  protected String name;
}

class Salesman extends Employee {...}
class Engineer extends Employee {...}
```

---

### Pull Up Constructor Body

```javascript
// Before
class Employee extends Party {
  constructor(name, id, monthlyCost) {
    super();
    this._id = id;
    this._name = name;
    this._monthlyCost = monthlyCost;
  }
}

// After
class Party {
  constructor(name){
    this._name = name;
  }
}

class Employee extends Party {
  constructor(name, id, monthlyCost) {
    super(name);
    this._id = id;
    this._monthlyCost = monthlyCost;
  }
}
```

---

### Push Down Method
**逆操作**: Pull Up Method

```javascript
// Before
class Employee {
  get quota {...}
}

class Engineer extends Employee {...}
class Salesman extends Employee {...}

// After
class Employee {...}

class Engineer extends Employee {...}

class Salesman extends Employee {
  get quota {...}
}
```

---

### Push Down Field
**逆操作**: Pull Up Field

```java
// Before
class Employee {
  private String quota;
}
class Engineer extends Employee {...}
class Salesman extends Employee {...}

// After
class Employee {...}
class Engineer extends Employee {...}
class Salesman extends Employee {
  protected String quota;
}
```

---

### Replace Type Code with Subclasses
**別名**: Extract Subclass, Replace Type Code with State/Strategy
**逆操作**: Remove Subclass

```javascript
// Before
function createEmployee(name, type) {
  return new Employee(name, type);
}

// After
function createEmployee(name, type) {
  switch (type) {
    case "engineer": return new Engineer(name);
    case "salesman": return new Salesman(name);
    case "manager":  return new Manager(name);
  }
}
```

---

### Remove Subclass
**別名**: Replace Subclass with Fields
**逆操作**: Replace Type Code with Subclasses

```javascript
// Before
class Person {
  get genderCode() {return "X";}
}
class Male extends Person {
  get genderCode() {return "M";}
}
class Female extends Person {
  get genderCode() {return "F";}
}

// After
class Person {
  get genderCode() {return this._genderCode;}
}
```

---

### Extract Superclass

```javascript
// Before
class Department {
  get totalAnnualCost() {...}
  get name() {...}
  get headCount() {...}
}

class Employee {
  get annualCost() {...}
  get name() {...}
  get id() {...}
}

// After
class Party {
  get name() {...}
  get annualCost() {...}
}

class Department extends Party {
  get annualCost() {...}
  get headCount() {...}
}

class Employee extends Party {
  get annualCost() {...}
  get id() {...}
}
```

---

### Collapse Hierarchy

```javascript
// Before
class Employee {...}
class Salesman extends Employee {...}

// After
class Employee {...}
```

---

### Replace Subclass with Delegate

```javascript
// Before
class Order {
  get daysToShip() {
    return this._warehouse.daysToShip;
  }
}

class PriorityOrder extends Order {
  get daysToShip() {
    return this._priorityPlan.daysToShip;
  }
}

// After
class Order {
  get daysToShip() {
    return (this._priorityDelegate)
      ? this._priorityDelegate.daysToShip
      : this._warehouse.daysToShip;
  }
}

class PriorityOrderDelegate {
  get daysToShip() {
    return this._priorityPlan.daysToShip;
  }
}
```

---

### Replace Superclass with Delegate
**別名**: Replace Inheritance with Delegation

```javascript
// Before
class Stack extends List {...}

// After
class Stack {
  constructor() {
    this._storage = new List();
  }
}
```

---

## その他

### Replace Magic Literal
**別名**: Replace Magic Number with Symbolic Constant

```javascript
// Before
function potentialEnergy(mass, height) {
  return mass * 9.81 * height;
}

// After
const STANDARD_GRAVITY = 9.81;
function potentialEnergy(mass, height) {
  return mass * STANDARD_GRAVITY * height;
}
```

---

### Substitute Algorithm
**別名**: Replace Algorithm

```javascript
// Before
function foundPerson(people) {
  for(let i = 0; i < people.length; i++) {
    if (people[i] === "Don") return "Don";
    if (people[i] === "John") return "John";
    if (people[i] === "Kent") return "Kent";
  }
  return "";
}

// After
function foundPerson(people) {
  const candidates = ["Don", "John", "Kent"];
  return people.find(p => candidates.includes(p)) || '';
}
```

---

## クイックリファレンス（全66手法）

| # | 手法名 | 別名 |
|---|--------|------|
| 1 | Change Function Declaration | Add Parameter, Change Signature, Remove Parameter, Rename Function, Rename Method |
| 2 | Change Reference to Value | - |
| 3 | Change Value to Reference | - |
| 4 | Collapse Hierarchy | - |
| 5 | Combine Functions into Class | - |
| 6 | Combine Functions into Transform | - |
| 7 | Consolidate Conditional Expression | - |
| 8 | Decompose Conditional | - |
| 9 | Encapsulate Collection | - |
| 10 | Encapsulate Record | Replace Record with Data Class |
| 11 | Encapsulate Variable | Encapsulate Field, Self-Encapsulate Field |
| 12 | Extract Class | - |
| 13 | Extract Function | Extract Method |
| 14 | Extract Superclass | - |
| 15 | Extract Variable | Introduce Explaining Variable |
| 16 | Hide Delegate | - |
| 17 | Inline Class | - |
| 18 | Inline Function | Inline Method |
| 19 | Inline Variable | Inline Temp |
| 20 | Introduce Assertion | - |
| 21 | Introduce Parameter Object | - |
| 22 | Introduce Special Case | Introduce Null Object |
| 23 | Move Field | - |
| 24 | Move Function | Move Method |
| 25 | Move Statements into Function | - |
| 26 | Move Statements to Callers | - |
| 27 | Parameterize Function | Parameterize Method |
| 28 | Preserve Whole Object | - |
| 29 | Pull Up Constructor Body | - |
| 30 | Pull Up Field | - |
| 31 | Pull Up Method | - |
| 32 | Push Down Field | - |
| 33 | Push Down Method | - |
| 34 | Remove Dead Code | - |
| 35 | Remove Flag Argument | Replace Parameter with Explicit Methods |
| 36 | Remove Middle Man | - |
| 37 | Remove Setting Method | - |
| 38 | Remove Subclass | Replace Subclass with Fields |
| 39 | Rename Field | - |
| 40 | Rename Variable | - |
| 41 | Replace Command with Function | - |
| 42 | Replace Conditional with Polymorphism | - |
| 43 | Replace Constructor with Factory Function | Replace Constructor with Factory Method |
| 44 | Replace Control Flag with Break | Remove Control Flag |
| 45 | Replace Derived Variable with Query | - |
| 46 | Replace Error Code with Exception | - |
| 47 | Replace Exception with Precheck | Replace Exception with Test |
| 48 | Replace Function with Command | Replace Method with Method Object |
| 49 | Replace Inline Code with Function Call | - |
| 50 | Replace Loop with Pipeline | - |
| 51 | Replace Magic Literal | Replace Magic Number with Symbolic Constant |
| 52 | Replace Nested Conditional with Guard Clauses | - |
| 53 | Replace Parameter with Query | Replace Parameter with Method |
| 54 | Replace Primitive with Object | Replace Data Value with Object, Replace Type Code with Class |
| 55 | Replace Query with Parameter | - |
| 56 | Replace Subclass with Delegate | - |
| 57 | Replace Superclass with Delegate | Replace Inheritance with Delegation |
| 58 | Replace Temp with Query | - |
| 59 | Replace Type Code with Subclasses | Extract Subclass, Replace Type Code with State/Strategy |
| 60 | Return Modified Value | - |
| 61 | Separate Query from Modifier | - |
| 62 | Slide Statements | Consolidate Duplicate Conditional Fragments |
| 63 | Split Loop | - |
| 64 | Split Phase | - |
| 65 | Split Variable | Remove Assignments to Parameters, Split Temp |
| 66 | Substitute Algorithm | - |

---

*このカタログは Martin Fowler「リファクタリング 第2版」に基づいています。*
*詳細な Motivation と Mechanics は書籍を参照してください。*
