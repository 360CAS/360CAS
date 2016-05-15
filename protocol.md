# Protocol for data communication over Mesh network
 - Assumes that a mesh network is available to broadcast these data packets to all users (vehicles).
# Location Awareness Data Packet Structure
 - Purpose is to broadcast to all local nodes the current position, status and intent of originating node.
## Data Structure
 - MessageID: A unique string to represent this data packet.
 - TimeStamp : ISO 8601 Date Format String
 - TransmitterID : unique INT 64bit
 - Location: Lat/Lon of vehicles current location
 - Location Isotropic Uncertainty: float in meters.
 - VehiclePriority: A unique integer specifying the priority of the vehicle in ties.
 - Loaded: Boolean specifying that the vehicle must be
 - RoutePlan: May be either:
   - String 'static' - to indicate non-moving vehicle
   - String 'route_<id>' - where <id> indicates a predefined statistical path known by all nodes
   - String 'offroute' - to indicate a vehicle is currently moving with no known destination
   - Waypoint List - A list of Lat/Lon tuples that defines the statistical path for this vehicle.
 - Velocity: [x,y,z] vector of meters/second average velocity over some predefined
             time period. (currently set at 5 seconds). Velocity is defined from
             the front and centre of the vehicle.
 - Vehicle Type: String, indicates the vehicle type of node, a predefined but extensible list of vehicle type codes, e.g.
   ['DZ', 'LV', 'HV', 'GR', 'PL'] (human version = ['dozer', 'light vehicle', 'heavy vehicle', 'grader', 'personell'])
 - Status: String, a predefined but extensible list of status codes. For example
   ['FP', 'AT', 'OV', 'SL', 'FS' ,'AW'] (human readable versions
    ['following path', 'approaching turn', 'overtaking', 'slow manoeuver's',
    'frequent stopping', 'advance warning'])
 - Priority Interaction List : List of other TransmitterID's,
      Based upon this nodes most recent recieved messages and calculations
      constructs a list of nodes that must be communicated with to avoid collisions.

# Route data structure
 - A route is defined as a series of way-points that are no more than 5 meters apart.
 - Routes form a shared knowledge by the network.
 - Each route has a unique identifier.
 - A route may
# Route update broadcast message
 - Changes to a route (for example the a dual carriage way to a single
   carriage way) or changes to a location (from a drop off point to a
   part of a route) are to be propogated via an Update data packet.
 - When a route or location is changed its given a new unique
   identifier and its old identifier becomes invalid. This allows the
   system to detect when a node has an out of date route map.
 - Route changes can occur in any of the following situations:
     1. A vehicle remains stationary and blocks a path.
     2. A monitoring station observes a stationary blockage (new dump or wall collapse)
     3. More than 2 vehicles slow down and deviate on the same path.


# Calculation of Priority Lists
 - Purpose is to determine which local nodes in the network must
   undergo some negotiation with querying node to avoid collisions.
 - This is a function that takes in the current nodes latest data
   packet plus all data packets recieved over the network and returns
   a list of nodes that the current node may interact with. This needs
   to be supplied by the protocol implementer.

# Negotiation and decision making
A brief outline of how the nodes communicate to avoid collision.

## Polling
 - If a node is identified on a priority list, then each node will attempt a
   direct communication with the nodes.
 - This will involve each node updating each other with their latest
   relevant information and checking then recomputing the priority
   list to ensure a collision is still possible.  ## Select Priority
 - If a collision is probable, then the implementer needs to specify a
   policy for avoidance. This will need the site rules to be used and
   likely comes with the route data.
 - Ties are determined by vehicle priority, highest number wins.
## Assign action
 - The highest priority node is allow right of way, and the second
   priority gives way untill the risk has passed.
 - The system also alerts the operator/driver about which action to
   take.  (Similar to moving the traffic lights into the cab).
