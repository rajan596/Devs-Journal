# Interview tips for LLD Round

- Clarify all the requirements and confirm with interviewer
    - Who are the users of our system ?
    - What we want to do ?
- Create flow and confirm with the interviewer
- Try to come up with class Entities
- Skip creating constructor / CRUD methods.
- We need to mention important method of our entities
- Write Production grade code
- Data Flow Discussion : Try simulating how Classed together with using methods of each other fulfill the required functionality.
- Discuss concurrency issues if it can happen
- Try to find If Design pattern can be used here and name the Design pattern. Brush up Design patterns before going to LLD Interview
- Design keeping SOLID principles in mind

## Defining the flow

An example of a Possible LLD flow

1. User comes to an ATM machine
2. User inserts Card
3. Card is validated by ATM
    3.1) Invalid card break the flow and shoe error message
4. User enters credentials