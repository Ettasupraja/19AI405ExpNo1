ExpNo 1 :Developing AI Agent with
 PEAS Description
 Name : ETTA SUPRAJA
 Register Number : 212223220022
 AIM:
 To find the PEAS description for the given AI problem and develop an AI
 agent.
 Theory
 Medicine prescribing agent:
 Such this agent prescribes medicine for fever (greater than 98.5 degrees)
 which we consider here as unhealthy, by the user temperature input, and
 another environment is rooms in the hospital (two rooms). This agent has to
 consider two factors one is room location and an unhealthy patient in a
 random room, the agent has to move from one room to another to check
 and treat the unhealthy person. The performance of the agent is calculated
 by incrementing performance and each time after treating in one room
 again it has to check another room so that the movement causes the agent
 to reduce its performance. Hence, agents prescribe medicine to unhealthy.
 PEAS DESCRIPTION:
 Agent
 Type
 Medicine
 prescribing
 agent
 Performance
 Treating
 unhealthy,
 agent
 movement
 Environment
 Rooms,
 Patient
 Actuators
 Medicine,
 Treatment
 Sensors
 Location,
 Temperatur
 of patient
 DESIGN STEPS
 STEP 1:Identifying the input:
 Temperature from patients, Location.
 STEP 2:Identifying the output:
Prescribe medicine if the patient in a random has a fever.
 PEAS description is developed by the performance, environment, actuators,
 and sensors in an agent.
 Treat unhealthy patients in each room. And check for the unhealthy patients
 in random room
 Measure the performance parameters: For each treatment performance
 incremented, for each movement performance decremented
 STEP 3:Developing the PEAS description:
 STEP 4:Implementing the AI agent:
 STEP 5:
 Program :

 import random
 class Thing:
    """
    This represents any physical object that can appear in an Environmen
    """
    def is_alive(self):
        """Things that are 'alive' should return true."""
        return hasattr(self, 'alive') and self.alive
    
    def show_state(self):
        """Display the agent's internal state. Subclasses should overrid
        print("I don't know how to show_state.")
 class Agent(Thing):
    """
    An Agent is a subclass of Thing
    """
    def __init__(self, program=None):
        self.alive = True
        self.performance = 0
        self.program = program
    
    def can_grab(self, thing):
        """Return True if this agent can grab this thing.
        Override for appropriate subclasses of Agent and Thing."""
        return False
 def TableDrivenAgentProgram(table):
    """
    [Figure 2.7]
    This agent selects an action based on the percept sequence.
    It is practical only for tiny domains.
    To customize it, provide as table a dictionary of all
    {percept_sequence:action} pairs.
    """
    percepts = []
    
    def program(percept):
        action = None
        percepts.append(percept)
        action = table.get(tuple(percepts))
        return action
    
    return program
 loc_A, loc_B = (0, 0), (1, 0) # The two locations for the Vacuum world
 def TableDrivenVacuumAgent():
    """
    Tabular approach towards vacuum world
    """
    table = {
        ((loc_A, 'Clean'),): 'Right',
        ((loc_A, 'Dirty'),): 'Suck',
        ((loc_B, 'Clean'),): 'Left',
        ((loc_B, 'Dirty'),): 'Suck',
        ((loc_A, 'Dirty'), (loc_A, 'Clean')): 'Right',
        ((loc_A, 'Clean'), (loc_B, 'Dirty')): 'Suck',
        ((loc_B, 'Clean'), (loc_A, 'Dirty')): 'Suck',
        ((loc_B, 'Dirty'), (loc_B, 'Clean')): 'Left',
        ((loc_A, 'Dirty'), (loc_A, 'Clean'), (loc_B, 'Dirty')): 'Suck',
        ((loc_B, 'Dirty'), (loc_B, 'Clean'), (loc_A, 'Dirty')): 'Suck'
    }
    return Agent(TableDrivenAgentProgram(table))
 class Environment:
    """Abstract class representing an Environment. 'Real' Environment cl
    inherit from this. Your Environment will typically need to implement
    percept: Define the percept that an agent sees.
    execute_action: Define the effects of executing an action.
    Also update the agent.performance slot.
    The environment keeps a list of .things and .agents (which is a subs
    of .things). Each agent has a .performance slot, initialized to 0.
    Each thing has a .location slot, even though some environments may n
    need this."""
    def __init__(self):
        self.things = []
        self.agents = []
    
    def percept(self, agent):
        """Return the percept that the agent sees at this point. (Implem
        raise NotImplementedError
    
    def execute_action(self, agent, action):
        """Change the world to reflect this action. (Implement this.)"""
        raise NotImplementedError
    
    def default_location(self, thing):
        """Default location to place a new thing with unspecified locati
        return None
    
    def is_done(self):
        """By default, we're done when we can't find a live agent."""
        return not any(agent.is_alive() for agent in self.agents)
    
    def step(self):
        """Run the environment for one time step. If the
        actions and exogenous changes are independent, this method will
        do. If there are interactions between them, you'll need to
        override this method."""
        if not self.is_done():
            actions = []
            for agent in self.agents:
                if agent.alive:
                    actions.append(agent.program(self.percept(agent)))
                else:
                    actions.append("")
            for (agent, action) in zip(self.agents, actions):
                self.execute_action(agent, action)
    
    def run(self, steps=1000):
        """Run the Environment for given number of time steps."""
        for step in range(steps):
            if self.is_done():
                return
            self.step()
    
    def add_thing(self, thing, location=None):
        """Add a thing to the environment, setting its location. For
        convenience, if thing is an agent program we make a new agent
        for it. (Shouldn't need to override this.)"""
        if not isinstance(thing, Thing):
            thing = Agent(thing)
        if thing in self.things:
            print("Can't add the same thing twice")
        else:
            thing.location = location if location is not None else self.
            self.things.append(thing)
            if isinstance(thing, Agent):
                thing.performance = 0
                self.agents.append(thing)
    
    def delete_thing(self, thing):
        """Remove a thing from the environment."""
        try:
            self.things.remove(thing)
        except ValueError as e:
            print(e)
            print(" in Environment delete_thing")
            print(" Thing to be removed: {} at {}".format(thing, thing.l
            print(" from list: {}".format([(thing, thing.location) for t
        if thing in self.agents:
            self.agents.remove(thing)
 class TrivialVacuumEnvironment(Environment):
    """This environment has two locations, A and B. Each can be Dirty
    or Clean. The agent perceives its location and the location's
    status. This serves as an example of how to implement a simple
    Environment."""
    def __init__(self):
        super().__init__()
        self.status = {
            loc_A: random.choice(['Clean', 'Dirty']),
            loc_B: random.choice(['Clean', 'Dirty'])
        }
    
    def thing_classes(self):
        return [TableDrivenVacuumAgent]
    
    def percept(self, agent):
        """Returns the agent's location, and the location status (Dirty/
        return agent.location, self.status[agent.location]
    
    def execute_action(self, agent, action):
        """Change agent's location and/or location's status; track perfo
        Score 10 for each dirt cleaned; -1 for each move."""
        if action == 'Right':
            agent.location = loc_B
            agent.performance -= 1
        elif action == 'Left':
            agent.location = loc_A
            agent.performance -= 1
        elif action == 'Suck':
            if self.status[agent.location] == 'Dirty':
                agent.performance += 10
                self.status[agent.location] = 'Clean'
    
    def default_location(self, thing):
        """Agents start in either location at random."""
        return random.choice([loc_A, loc_B])
 if __name__ == "__main__":
    agent = TableDrivenVacuumAgent()
    environment = TrivialVacuumEnvironment()
    environment.add_thing(agent)
    print(environment.status)
    environment.run(steps=10)
print(environment.status)
 print(agent.performance)
 Output :
 ![WhatsApp Image 2024-11-17 at 17 38 38_bb468b24](https://github.com/user-attachments/assets/087f2478-2daa-4b04-b246-ca957416ee64)

 Result :
 The AI agent is developed successfully
