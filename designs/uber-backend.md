# Uber Backend

## Summary
![overview](../img/uber-backend-overview.png)

## Requirements and Goals of the System
There are two kinds of users in the system: drivers and customers.
- Drivers need to regularly notify the service about their current location and their availability to pick passengers.
- Customers get to see all the nearby available drivers.
- Customer can request a ride; nearby drivers are notified that a customer is ready to be picked up.
- Once a driver and customer accept a ride, they can constantly see each other’s current location, until the trip finishes.
- Upon reaching the destination, the driver marks the journey complete to become available for the next ride.

## Capacity Estimation and Constraints
- Assume 300M customers and 1M drivers
- Assume 1M daily active customers and 500K daily active drivers.
- Assume 1M daily rides.
- Assume that all active drivers notify their current location every three seconds.
- Once a customer puts a request for a ride, the system should be able to contact drivers in real-time.

## Basic System Design and Algorithm
- Based on [Yelp design](yelp.md).
- Biggest difference: QuadTree with frequent updates
- Two issues with the Dynamic Grid solution
  - All active drivers are reporting their locations every three seconds. Need to update the data structures to reflect that, which can be costly.
  - Need to quickly propagate the current location of all the nearby drivers to any active customer in that area.

- Do we need to modify our QuadTree every time a driver reports their location?
  - Since all active drivers report their location every three seconds, therefore there will be a lot more updates happening to our tree than querying for nearby drivers.
  - We can keep the latest position reported by all drivers in a hash table (`DriverLocationHT`) and update our QuadTree a little less frequent.
  - Assume we guarantee that a driver’s current location will be reflected in the QuadTree within 15 seconds.

- Memory for `DriverLocationHT`?
  - Each driver: 35 bytes
    - Driver ID: 3B
    - Old latitude: 8B
    - Old longitude: 8B
    - New latitude: 8B
    - Old longitude: 8B
  - 1 M drivers * 35 B = 35 MB

- Bandwidth for receiving driver location updates?
  - Each driver: `3 + 8 * 2 = 19 B`
  - 1M drivers * 19 B = 19 MB per three seconds

- Distribute `DriverLocationHT` onto multiple servers
  - 











