# Chapters
## Comments
### It's better to create meaningful function / class property than adding comments  

 ``` java
// check if employee is eligible for full benefits  
 if ((employ.flags & HOURLY_FLAG) && (employee.age > 65))  
```
 VS  
 ``` java
 if (employee.isEligibleForFullBenefits)
```
### Add comments about some consequences, eg. when test is set to skip, because it needs an hour to run.
### Adding TODO comments is okay - but remember to go thorugh them and do the work from time to time.
### It's good to add comments when writing public API 
### All comments that require going through some different system places to understand it are BAD
### Never leave commented code - people won't have enough courage to delete it. It will probably stay there forever / or long time

## Error handling
### Use try catch instead of if else if possible
Separate algorithm logic from error handling:  

``` java
public void sendShutDown() {
  try {
    tryToShutDown();
  } catch (DeviceShutDownError e) {
    logger.log(e);
}
private void tryToShutDown() throws DeviceShutDownError {
  // ..
}
```


### Write try - catch - finally first
Write test that force exceptions and add behavior to satisfy your test.  
try catch are like transactions

### Bring context by using exceptions
Exceptions should have context about error. They should carry enought information.

### Defining class to handle exceptions
It could be beneifcial to declare class only to handle multiple exceptions from e.g. external library.
Example:  
``` java
public class LocalPort {
// ..
public LocalPort(int portNumber) {
 innerPort = new ACMEPort(portNumber);
}
public void open() {
 try{
 innerPort.open();
 } catch (DeviceResponseException e) {
  throw new PortDeviceFailure(e);
 } catch (ATM1212UnlockedException e) {
  throw new PortDeviceFailure(e);
 } catch (GMXError e) {
  throw new PortDeviceFailure(e);
 }
}
}
```
### Define normal flow
We could use SPECIAL CASE PATTERN to handle special case for us.
Example:
``` java
try {
  MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
  m_total += expenses.getTotal();
} catch(MealExpensesNotFound e) {
  m_total += getMealPerDiem();
}
```
We could move catching exception to expenseReportDAO class and when that happens we return getTotal() from newly created class:
``` java
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
// the Exception is handled in the DAO and getMails returns
// PerDiemMealExpenses if an error is thrown...
public class PerDiemMealExpenses implements MealExpenses {
 public int getTotal() {
 // return the per diem default
 }
}

```

### Don't return Null / None (Python)
It creates unnecessary issues like "None type object ..."  
Example:
method returns Null, so we have to check if it's != null, that created additional logic:
``` java
List<Employee> employees = getEmployees();
if (employees != null) {
  for(Employee e : employees) {
    totalPay += e.getPay();
  }
}
```
Instead we can change getEmployess() method to return empty list instead of Null:
``` java
List<Employee> employees = getEmployees();
for(Employee e : employees) {
  totalPay += e.getPay();
}
```

### Don't pass Null as argument
We should avoid passing Null to methods if that's possible. A Null argument is indication of a problem.
One possible way could be to make assertions:
Example:  
``` java
public class MetricsCalculator
{
 public double xProjection(Point p1, Point p2) {
  assert p1 != null : "p1 nie może być null";
  assert p2 != null : "p2 nie może być null";
  return (p2.x - p1.x) * 1.5;
 }
}
```

## Unit tests
### 3 laws of TDD
1st: You may not write production code until you have written a failing unit test. (first write failing unit tests)
2nd: You may not write more of a unit test than is sufficient to fail, and not compiling is failing. (there are not more failing tests that could be written)  
3rd: You may not write more production code than is sufficient to pass the currently failing test. (no more production code until you write more failing tests)  

By doing this we are protecting us by writing many unit tests along with production code. A dozen tests a day, hundreds tests a month, thousands tests a year.

### Keep the tests clean
Unit tests just like production code should be keep clean. When writing thuosands tests and not keeping them clean we create super complex system. Even small changes in that code will end up beign dreadful.  **Test code is just as important as production code**

### Tests create more possibilities
By having unit tests our production system is more flexible. We're not afraid of making changes, because of unit tests that protect us from production failure.  
Tests are the key to keep system in the good shape.

### Clean unit tests
The test code must be designed to be read.  
Example:
``` java
public void testGetPageHieratchyAsXml() throws Exception
{
	crawler.addPage(root, PathParser.parse("PageOne"));
	crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
	crawler.addPage(root, PathParser.parse("PageTwo"));

	request.setResource("root");
	request.addInput("type", "pages");
	Responder responder = new SerializedPageResponder();
	SimpleResponse response =
		(SimpleResponse) responder.makeResponse(
				new FitNesseContext(root), request);
	String xml = response.getContent();

	assertEquals("text/xml", response.getContentType());
	assertSubString("<name>PageOne</name>", xml);
	assertSubString("<name>PageTwo</name>", xml);
	assertSubString("<name>ChildOne</name>", xml);
}
```
After refactor:  
``` java
public void testGetPageHierarchyAsXml() throws Exception {
	makePages("PageOne", "PageOne.ChildOne", "PageTwo");

	submitRequest("root", "type:pages");

	assertResponseIsXML();
	assertResponseContains(
			"<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
	);
}
```

BUILD-OPERATE-CHECK pattern:  
- Build: the first part builds up the test data.
- Operate: the second part operates on that test data.
- Check: the third part checks that the operation yielded the expected results

### Dual standards
In the code bellow it's hard to tell what tic function does. Reading assert is also not that easy, first checking state and then moving the the left and checking assert true or false. 

``` java
public void turnOnLoTempAlarmAtThreshold() throws Exception {
	hw.setTemp(WAY_TO_COLD);
	controller.tic();
	assertTrue(hw.heaterState());
	assertTrue(hw.blowerState());
	assertTrue(hw.coolerState());
	assertTrue(hw.hiTempAlarm());
	assertTrue(hw.loTempAlaram());
}
```

We can simply translates it into this:
``` java
public void turnOnLoTempAlarmAtThreshold() throws Exception {
	wayToCold();
	assertEquals("HBchL", hw.getState());
}
```
capitalization in assert is used for ON / OFF. Capital letter means turned ON and lowercase means turned OFF.
