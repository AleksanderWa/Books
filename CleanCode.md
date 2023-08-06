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
