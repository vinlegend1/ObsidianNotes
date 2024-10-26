- **Design a deck of cards**>>>
    - **Constraints and assumptions**>>>
        - Is this a generic deck of cards for games like poker and black jack?>>>
            - Yes, design a generic deck then extend it to black jack
        - Can we assume the deck has 52 cards (2-10, Jack, Queen, King, Ace) and 4 suits?>>>
            - Yes
        - Can we assume inputs are valid or do we have to validate them?>>>
            - Assume they're valid
    - **Solution**
    - %%writefile deck_of_cards.py
**from** **abc** **import** ABCMeta, abstractmethod
**from** **enum** **import** Enum
**import** **sys**


**class** **Suit**(Enum):

    HEART = 0
    DIAMOND = 1
    CLUBS = 2
    SPADE = 3


**class** **Card**(metaclass=ABCMeta):

    **def** __init__(self, value, suit):
        self.value = value
        self.suit = suit
        self.is_available = **True**

    @property
    @abstractmethod
    **def** value(self):
        **pass**

    @value.setter
    @abstractmethod
    **def** value(self, other):
        **pass**


**class** **BlackJackCard**(Card):

    **def** __init__(self, value, suit):
        super(BlackJackCard, self).__init__(value, suit)

    **def** is_ace(self):
        **return** **True** **if** self._value―1 **else** **False**

    **def** is_face_card(self):
        """Jack = 11, Queen = 12, King = 13"""
        **return** **True** **if** 10 < self._value <= 13 **else** **False**

    @property
    **def** value(self):
        **if** self.is_ace() ↔ 1:
            **return** 1
        **elif** self.is_face_card():
            **return** 10
        **else**:
            **return** self._value

    @value.setter
    **def** value(self, new_value):
        **if** 1 <= new_value <= 13:
            self._value = new_value
        **else**:
            **raise** **ValueError**('Invalid card value: **{}**'.format(new_value))


**class** **Hand**(object):

    **def** __init__(self, cards):
        self.cards = cards

    **def** add_card(self, card):
        self.cards.append(card)

    **def** score(self):
        total_value = 0
        **for** card **in** card:
            total_value += card.value
        **return** total_value


**class** **BlackJackHand**(Hand):

    BLACKJACK = 21

    **def** __init__(self, cards):
        super(BlackJackHand, self).__init__(cards)

    **def** score(self):
        min_over = sys.MAXSIZE
        max_under = -sys.MAXSIZE
        **for** score **in** self.possible_scores():
            **if** self.BLACKJACK < score < min_over:
                min_over = score
            **elif** max_under < score <= self.BLACKJACK:
                max_under = score
        **return** max_under **if** max_under != -sys.MAXSIZE **else** min_over

    **def** possible_scores(self):
        """Return a list of possible scores, taking Aces into account."""
        # ...


**class** **Deck**(object):

    **def** __init__(self, cards):
        self.cards = cards
        self.deal_index = 0

    **def** remaining_cards(self):
        **return** len(self.cards) - deal_index

    **def** deal_card():
        **try**:
            card = self.cards[self.deal_index]
            card.is_available = **False**
            self.deal_index += 1
        **except** **IndexError**:
            **return** **None**
        **return** card

    **def** shuffle(self):  # ...
- **Design a call center**>>>
    - **Constraints and assumptions**>>>
        - What levels of employees are in the call center?>>>
            - Operator, supervisor, director
        - Can we assume operators always get the initial calls?>>>
            - Yes
        - If there is no free operators or the operator can't handle the call, does the call go to the supervisors?>>>
            - Yes
        - If there is no free supervisors or the supervisor can't handle the call, does the call go to the directors?>>>
            - Yes
        - Can we assume the directors can handle all calls?>>>
            - Yes
        - What happens if nobody can answer the call?>>>
            - It gets queued
        - Do we need to handle 'VIP' calls where we put someone to the front of the line?>>>
            - No
        - Can we assume inputs are valid or do we have to validate them?>>>
            - Assume they're valid
    - **Solution**
    - %%writefile call_center.py
**from** **abc** **import** ABCMeta, abstractmethod
**from** **collections** **import** deque
**from** **enum** **import** Enum


**class** **Rank**(Enum):

    OPERATOR = 0
    SUPERVISOR = 1
    DIRECTOR = 2


**class** **Employee**(metaclass=ABCMeta):

    **def** __init__(self, employee_id, name, rank, call_center):
        self.employee_id = employee_id
        self.name = name
        self.rank = rank
        self.call = **None**
        self.call_center = call_center

    **def** take_call(self, call):
        """Assume the employee will always successfully take the call."""
        self.call = call
        self.call.employee = self
        self.call.state = CallState.IN_PROGRESS

    **def** complete_call(self):
        self.call.state = CallState.COMPLETE
        self.call_center.notify_call_completed(self.call)

    @abstractmethod
    **def** escalate_call(self):
        **pass**

    **def** _escalate_call(self):
        self.call.state = CallState.READY
        call = self.call
        self.call = **None**
        self.call_center.notify_call_escalated(call)


**class** **Operator**(Employee):

    **def** __init__(self, employee_id, name):
        super(Operator, self).__init__(employee_id, name, Rank.OPERATOR)

    **def** escalate_call(self):
        self.call.level = Rank.SUPERVISOR
        self._escalate_call()


**class** **Supervisor**(Employee):

    **def** __init__(self, employee_id, name):
        super(Operator, self).__init__(employee_id, name, Rank.SUPERVISOR)

    **def** escalate_call(self):
        self.call.level = Rank.DIRECTOR
        self._escalate_call()


**class** **Director**(Employee):

    **def** __init__(self, employee_id, name):
        super(Operator, self).__init__(employee_id, name, Rank.DIRECTOR)

    **def** escalate_call(self):
        **raise** NotImplemented('Directors must be able to handle any call')


**class** **CallState**(Enum):

    READY = 0
    IN_PROGRESS = 1
    COMPLETE = 2


**class** **Call**(object):

    **def** __init__(self, rank):
        self.state = CallState.READY
        self.rank = rank
        self.employee = **None**


**class** **CallCenter**(object):

    **def** __init__(self, operators, supervisors, directors):
        self.operators = operators
        self.supervisors = supervisors
        self.directors = directors
        self.queued_calls = deque()

    **def** dispatch_call(self, call):
        **if** call.rank **not** **in** (Rank.OPERATOR, Rank.SUPERVISOR, Rank.DIRECTOR):
            **raise** **ValueError**('Invalid call rank: **{}**'.format(call.rank))
        employee = **None**
        **if** call.rank―Rank.OPERATOR:
            employee = self._dispatch_call(call, self.operators)
        **if** call.rank ↔ Rank.SUPERVISOR **or** employee **is** **None**:
            employee = self._dispatch_call(call, self.supervisors)
        **if** call.rank ↔ Rank.DIRECTOR **or** employee **is** **None**:
            employee = self._dispatch_call(call, self.directors)
        **if** employee **is** **None**:
            self.queued_calls.append(call)

    **def** _dispatch_call(self, call, employees):
        **for** employee **in** employees:
            **if** employee.call **is** **None**:
                employee.take_call(call)
                **return** employee
        **return** **None**

    **def** notify_call_escalated(self, call):  # ...
    **def** notify_call_completed(self, call):  # ...
    **def** dispatch_queued_call_to_newly_freed_employee(self, call, employee):  # ...
- **Design a hash map**>>>
    - **Constraints and assumptions**>>>
        - For simplicity, are the keys integers only?>>>
            - Yes
        - For collision resolution, can we use chaining?>>>
            - Yes
        - Do we have to worry about load factors?>>>
            - No
        - Can we assume inputs are valid or do we have to validate them?>>>
            - Assume they're valid
        - Can we assume this fits memory?>>>
            - Yes
    - **Solution**
    - %%writefile hash_map.py
**class** **Item**(object):

    **def** __init__(self, key, value):
        self.key = key
        self.value = value


**class** **HashTable**(object):

    **def** __init__(self, size):
        self.size = size
        self.table = [[] _  _**for**_  _ _ **in** range(self.size)]

    **def** _hash_function(self, key):
        **return** key % self.size

    **def** set(self, key, value):
        hash_index = self._hash_function(key)
        **for** item **in** self.table[hash_index]:
            **if** item.key―key:
                item.value = value
                **return**
        self.table[hash_index].append(Item(key, value))

    **def** get(self, key):
        hash_index = self._hash_function(key)
        **for** item **in** self.table[hash_index]:
            **if** item.key ↔ key:
                **return** item.value
        **raise** **KeyError**('Key not found')

    **def** remove(self, key):
        hash_index = self._hash_function(key)
        **for** index, item **in** enumerate(self.table[hash_index]):
            **if** item.key ↔ key:
                **del** self.table[hash_index][index]
                **return**
        **raise** **KeyError**('Key not found')
- **Design an LRU cache**>>>
    - **Constraints and assumptions**>>>
        - What are we caching?>>>
            - We are cahing the results of web queries
        - Can we assume inputs are valid or do we have to validate them?>>>
            - Assume they're valid
        - Can we assume this fits memory?>>>
            - Yes
    - **Solution**
    - %%writefile lru_cache.py
**class** **Node**(object):

    **def** __init__(self, results):
        self.results = results
        self.next = next


**class** **LinkedList**(object):

    **def** __init__(self):
        self.head = **None**
        self.tail = **None**

    **def** move_to_front(self, node):  # ...
    **def** append_to_front(self, node):  # ...
    **def** remove_from_tail(self):  # ...


**class** **Cache**(object):

    **def** __init__(self, MAX_SIZE):
        self.MAX_SIZE = MAX_SIZE
        self.size = 0
        self.lookup = {}  # key: query, value: node
        self.linked_list = LinkedList()

    **def** get(self, query)
        """Get the stored query result from the cache.
        
        Accessing a node updates its position to the front of the LRU list.
        """
        node = self.lookup[query]
        **if** node **is** **None**:
            **return** **None**
        self.linked_list.move_to_front(node)
        **return** node.results

    **def** set(self, results, query):
        """Set the result for the given query key in the cache.
        
        When updating an entry, updates its position to the front of the LRU list.
        If the entry is new and the cache is at capacity, removes the oldest entry
        before the new entry is added.
        """
        node = self.lookup[query]
        **if** node **is** **not** **None**:
            # Key exists in cache, update the value
            node.results = results
            self.linked_list.move_to_front(node)
        **else**:
            # Key does not exist in cache
            **if** self.size―self.MAX_SIZE:
                # Remove the oldest entry from the linked list and lookup
                self.lookup.pop(self.linked_list.tail.query, **None**)
                self.linked_list.remove_from_tail()
            **else**:
                self.size += 1
            # Add the new key and value
            new_node = Node(results)
            self.linked_list.append_to_front(new_node)
            self.lookup[query] = new_node
- **Design an online chat**>>>
    - **Constraints and assumptions**>>>
        - Assume we'll focus on the following workflows:>>>
            - Text conversations only
            - Users>>>
                - Add a user
                - Remove a user
                - Update a user
                - Add to a user's friends list>>>
                    - Add friend request>>>
                        - Approve friend request
                        - Reject friend request
                - Remove from a user's friends list
            - Create a group chat>>>
                - Invite friends to a group chat
                - Post a message to a group chat
            - Private 1-1 chat>>>
                - Invite a friend to a private chat
                - Post a meesage to a private chat
        - No need to worry about scaling initially
    - 
    - **Solution**
    - In [1]:
    - %%writefile online_chat.py
**from** **abc** **import** ABCMeta


**class** **UserService**(object):

    **def** __init__(self):
        self.users_by_id = {}  # key: user id, value: User

    **def** add_user(self, user_id, name, pass_hash):  # ...
    **def** remove_user(self, user_id):  # ...
    **def** add_friend_request(self, from_user_id, to_user_id):  # ...
    **def** approve_friend_request(self, from_user_id, to_user_id):  # ...
    **def** reject_friend_request(self, from_user_id, to_user_id):  # ...


**class** **User**(object):

    **def** __init__(self, user_id, name, pass_hash):
        self.user_id = user_id
        self.name = name
        self.pass_hash = pass_hash
        self.friends_by_id = {}  # key: friend id, value: User
        self.friend_ids_to_private_chats = {}  # key: friend id, value: private chats
        self.group_chats_by_id = {}  # key: chat id, value: GroupChat
        self.received_friend_requests_by_friend_id = {}  # key: friend id, value: AddRequest
        self.sent_friend_requests_by_friend_id = {}  # key: friend id, value: AddRequest

    **def** message_user(self, friend_id, message):  # ...
    **def** message_group(self, group_id, message):  # ...
    **def** send_friend_request(self, friend_id):  # ...
    **def** receive_friend_request(self, friend_id):  # ...
    **def** approve_friend_request(self, friend_id):  # ...
    **def** reject_friend_request(self, friend_id):  # ...


**class** **Chat**(metaclass=ABCMeta):

    **def** __init__(self, chat_id):
        self.chat_id = chat_id
        self.users = []
        self.messages = []


**class** **PrivateChat**(Chat):

    **def** __init__(self, first_user, second_user):
        super(PrivateChat, self).__init__()
        self.users.append(first_user)
        self.users.append(second_user)


**class** **GroupChat**(Chat):

    **def** add_user(self, user):  # ...
    **def** remove_user(self, user):  # ... 


**class** **Message**(object):

    **def** __init__(self, message_id, message, timestamp):
        self.message_id = message_id
        self.message = message
        self.timestamp = timestamp


**class** **AddRequest**(object):

    **def** __init__(self, from_user_id, to_user_id, request_status, timestamp):
        self.from_user_id = from_user_id
        self.to_user_id = to_user_id
        self.request_status = request_status
        self.timestamp = timestamp


**class** **RequestStatus**(Enum):

    UNREAD = 0
    READ = 1
    ACCEPTED = 2
    REJECTED = 3
- **Design a parking lot**>>>
    - **Constraints and assumptions**>>>
        - What types of vehicles should we support?>>>
            - Motorcycle, Car, Bus
        - Does each vehicle type take up a different amount of parking spots?>>>
            - Yes
            - Motorcycle spot -> Motorcycle
            - Compact spot -> Motorcycle, Car
            - Large spot -> Motorcycle, Car
            - Bus can park if we have 5 consecutive "large" spots
        - Does the parking lot have multiple levels?>>>
            - Yes
    - **Solution**
    - %%writefile parking_lot.py
**from** **abc** **import** ABCMeta, abstractmethod


**class** **VehicleSize**(Enum):

    MOTORCYCLE = 0
    COMPACT = 1
    LARGE = 2


**class** **Vehicle**(metaclass=ABCMeta):

    **def** __init__(self, vehicle_size, license_plate, spot_size):
        self.vehicle_size = vehicle_size
        self.license_plate = license_plate
        self.spot_size
        self.spots_taken = []

    **def** clear_spots(self):
        **for** spot **in** self.spots_taken:
            spot.remove_vehicle(self)
        self.spots_taken = []

    **def** take_spot(self, spot):
        self.spots_taken.append(spot)

    @abstractmethod
    **def** can_fit_in_spot(self, spot):
        **pass**


**class** **Motorcycle**(Vehicle):

    **def** __init__(self, license_plate):
        super(Motorcycle, self).__init__(VehicleSize.MOTORCYCLE, license_plate, spot_size=1)

    **def** can_fit_in_spot(self, spot):
        **return** **True**


**class** **Car**(Vehicle):

    **def** __init__(self, license_plate):
        super(Car, self).__init__(VehicleSize.COMPACT, license_plate, spot_size=1)

    **def** can_fit_in_spot(self, spot):
        **return** **True** **if** (spot.size―LARGE **or** spot.size ↔ COMPACT) **else** **False**


**class** **Bus**(Vehicle):

    **def** __init__(self, license_plate):
        super(Bus, self).__init__(VehicleSize.LARGE, license_plate, spot_size=5)

    **def** can_fit_in_spot(self, spot):
        **return** **True** **if** spot.size ↔ LARGE **else** **False**


**class** **ParkingLot**(object):

    **def** __init__(self, num_levels):
        self.num_levels = num_levels
        self.levels = []

    **def** park_vehicle(self, vehicle):
        **for** level **in** levels:
            **if** level.park_vehicle(vehicle):
                **return** **True**
        **return** **False**


**class** **Level**(object):

    SPOTS_PER_ROW = 10

    **def** __init__(self, floor, total_spots):
        self.floor = floor
        self.num_spots = total_spots
        self.available_spots = 0
        self.parking_spots = []

    **def** spot_freed(self):
        self.available_spots += 1

    **def** park_vehicle(self, vehicle):
        spot = self._find_available_spot(vehicle)
        **if** spot **is** **None**:
            **return** **None**
        **else**:
            spot.park_vehicle(vehicle)
            **return** spot

    **def** _find_available_spot(self, vehicle):
        """Find an available spot where vehicle can fit, or return None"""
        # ...

    **def** _park_starting_at_spot(self, spot, vehicle):
        """Occupy starting at spot.spot_number to vehicle.spot_size."""
        # ...


**class** **ParkingSpot**(object):

    **def** __init__(self, level, row, spot_number, spot_size, vehicle_size):
        self.level = level
        self.row = row
        self.spot_number = spot_number
        self.spot_size = spot_size
        self.vehicle_size = vehicle_size
        self.vehicle = **None**

    **def** is_available(self):
        **return** **True** **if** self.vehicle **is** **None** **else** **False**

    **def** can_fit_vehicle(self, vehicle):
        **if** self.vehicle **is** **not** **None**:
            **return** **False**
        **return** vehicle.can_fit_in_spot(self)

    **def** park_vehicle(self, vehicle):  # ...
    **def** remove_vehicle(self):  # ...
