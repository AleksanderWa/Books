# Chapters
## Comments
It's better to create meaningful function / class property than adding comments
 '''
 // check if employee is eligible for full benefits
 if ((employ.flags & HOURLY_FLAG) && (employee.age > 65))
 '''
 VS
 '''
 if (employee.isEligibleForFullBenefits)
 '''