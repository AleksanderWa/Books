# Chapters
## Comments
### It's better to create meaningful function / class property than adding comments  

 ```
// check if employee is eligible for full benefits  
 if ((employ.flags & HOURLY_FLAG) && (employee.age > 65))  
```
 VS  
 ```
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

```
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
```
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
```
try {
  MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
  m_total += expenses.getTotal();
} catch(MealExpensesNotFound e) {
  m_total += getMealPerDiem();
}
```
We could move catching exception to expenseReportDAO class and when that happens we return getTotal() from newly created class:
```
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
