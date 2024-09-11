# Design a Parking Lot

## Requirements
- There can be multiple parkings in the city
- A parking lot can have multiple floors or a single floor
- Can have multiple entrances and exits
- Can have N no of empty slots. Each slot can be for a specific vehicle type
- Slot can be available, occupied, maintainance, not available
- Optimisations
    - We need to assign nearest location from entrance / exit based on requirement
- User need to take ticket and need to calculate charges based on hourly rates. Diff for diff kind of vehicles
- User need to pay at exit. He can pay using any available form of payment
- There will be board where we need to show avilable free slots. If full need to indicate that as well
- Reserved slots for electric cars. These spots should have charging point. customer can charge and pay.
- Each floor should have display board 

## Flow
- Customer comes at entry gate with vehicle
- entry gate gives nearest parking slot to park and ticket mentioning entry time
- customer heads to exit gate -> gives ticket -> Exit gate calculates charges
- customer pays the charges and exits
- slot becomes available again

## Entities
- Entry Gate, Exit gate
- Vehicle - varioue types
- Parking slot
- Ticket
- Parking
- Location
- Payment
- Floor

## LLD
```java
package com.uber;

import java.util.List;
import java.util.Map;
import java.util.concurrent.Semaphore;

class ParkingLot {
    Map<String, EntryGate> entryGates;
    Map<String, EntryGate> exitGates;
    List<ParkingFloor> parkingFloors;
}

class ParkingFloor {
    List<ParkingSlot> slots;
    ParkingDisplayBoard parkingDisplayBoard;
    CustomerInfoPortal customerInfoPortal;
}

class ParkingDisplayBoard {
    void displayEmptySlots(){}
}
class CustomerInfoPortal{
    void makePayment(Ticket ticket) {}
}
class EntryGate {
    /* Most Important part of our system */
    ParkingSlot assignSlot(Vehicle vehicle){return null;}
    Ticket generateTicket(Vehicle vehicle){return null;}
}
class ExitGate {
    void makePayment(Ticket ticket) {}
    void markSlotAvailable(ParkingSlot slot){}
}
class ParkingSlot {
    ParkingSlotStatus parkingSlotStatus;
}
class CarParkingSlot extends ParkingSlot{}
class MotorCycleParkingSlot extends ParkingSlot{}
class ElectricParkingSlot extends ParkingSlot{
    ElectricPanel panel;
}
class ElectricPanel {
    long chargingStartTime;
    Payment payment; // to make payment for charging
    void endCharging(){}
}
enum ParkingSlotStatus {
    AVAILABLE,
    RESERVED,
    OCCUPIED,
    UNDER_MAINTANANCE
}
class Ticket {
    long entryTime;
    long exitTime;
    Vehicle vehicle;
    ParkingSlot parkingSlot;
    long charge;
    TicketStatus ticketStatus;
    Payment payment;
}
enum TicketStatus {
    PAID,
    ACTIVE
}
class Vehicle {
    String numberPlate;
    VehicleType vehicleType;

    public Vehicle(String numberPlate, VehicleType vehicleType) {
        this.numberPlate = numberPlate;
        this.vehicleType = vehicleType;
    }
}
enum VehicleType {
    TWO_WHEELER,
    CAR,
    TRUCK,
    CYCLE
}
class Payment {
    long paymentTime;
    PaymentStatus paymentStatus;
    int amount;

    void makePayment(){}
}
enum PaymentStatus {
    INITIATED,
    COMPLETED,
    FAILED
}
class CreditCardPayment extends Payment {
    String nameOnCard;
    void makePayment(){
        System.out.println("Making payment for ");
    }
}
enum Strategy {
    NEAR_ENTRY,
    NEAR_EXIT,
    NEAR_ELEVATOR,
    DEFAULT
}
class ParkingLotAllocationStrategyManager {
    Semaphore lock = new Semaphore(1);

    public ParkingSlot findSlot(Vehicle vehicle, Strategy strategy){
        try {
            lock.acquire(); // Need locks to maintain concurrency between 2 allocations
            switch (strategy){
                case DEFAULT:
                    return new DefaultParkingLotAllocationStrategy().findSlot(vehicle);
                case NEAR_ENTRY:
                    return new NearEntryParkingLotAllocationStrategy().findSlot(vehicle);
                case NEAR_ELEVATOR:
                    return new NearElevatorParkingLotAllocationStrategy().findSlot(vehicle);
            }
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            lock.release();
        }
        return null;
    }
}
interface ParkingLotAllocationStrategy {
    public ParkingSlot findSlot(Vehicle vehicle);
}
class DefaultParkingLotAllocationStrategy implements ParkingLotAllocationStrategy {
    @Override
    public ParkingSlot findSlot(Vehicle vehicle) {
        return null;
    }
}
class NearEntryParkingLotAllocationStrategy implements ParkingLotAllocationStrategy {
    @Override
    public ParkingSlot findSlot(Vehicle vehicle) {
        return null;
    }
}
class NearElevatorParkingLotAllocationStrategy implements ParkingLotAllocationStrategy {
    @Override
    public ParkingSlot findSlot(Vehicle vehicle) {
        return null;
    }
}
```

## Important Points
- Maintain **concurrency** when multiple entry points try to allocate same spot
- ParkingStrategies [Strategy Design Pattern]. This is important part as interviewer might ask to implement one of the strategy
    - We can have different parking startegies like
        - Default
        - NearElevator
        - NearEntry
- Cost computations strategy [Strategy Design Pattern]
    - hourly basis
    - minute basis
    - Fixed

## References
You may refer below resources for in-depth LLD design
- https://github.com/tssovi/grokking-the-object-oriented-design-interview/blob/master/object-oriented-design-case-studies/design-a-parking-lot.md
- https://dev.to/ssd/parking-lot-system-low-level-design-30gi