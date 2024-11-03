"""
World Of Planisuss Simulation

This simulation creates a virtual world where different types of animals interact with each other and their environment.
The main objective is to observe the behaviors and population dynamics of the animal species in the simulated world.

Classes:
- Vegetob: Represents the vegetation density in the world.
- Erbast: Represents the Erbast animal species.
- Carviz: Represents the Carviz animal species.
- Spot: Represents a spot in the world that can contain animals and vegetation.

Functions:
- move: Handles the movement of objects between spots in the world.
- create_spots: Creates the initial grid of spots in the world.
- simulate_day: Simulates a day in the world, including animal growth, movement, and interactions.
- display_world: Visualizes the current state of the world using matplotlib.
- plot_animal_counts: Plots the counts of different animal types in the world.
- update_world: Updates the world simulation for each frame of the animation.
- animate_world: Creates and displays the animation of the world simulation.
- validate_input: Validates user input for the number of rows, columns, and days.

"""

# Import necessary modules




import matplotlib.pyplot as plt
import numpy as np
import matplotlib.animation as animation
import matplotlib.gridspec as gridspec
class Vegetob:
    """
    Represents the vegetation density in the world.

    Attributes:
    - density: The current density of vegetation.
    """

    def __init__(self):
        """
        Initializes a new instance of the Vegetob class.
        The density is randomly assigned.
        """
        self.density = np.random.randint(80)


class Erbast:
    """
    Represents the Erbast animal species.

    Attributes:
    - energy: The energy level of the Erbast.
    - age: The age of the Erbast.
    - lifetime: The maximum lifespan of the Erbast.
    - social: The social behavior factor of the Erbast.
    """

    def __init__(self, energy=17, lifetime=25):
        """
        Initializes a new instance of the Erbast class.

        Args:
        - energy: The initial energy level of the Erbast. Default is 17.
        - lifetime: The maximum lifespan of the Erbast. Default is 25.
        """
        self.energy = energy
        self.age = 0
        self.lifetime = lifetime
        self.social = round(np.random.random(), 3)


class Carviz:
    """
    Represents the Carviz animal species.

    Attributes:
    - energy: The energy level of the Carviz.
    - age: The age of the Carviz.
    - lifetime: The maximum lifespan of the Carviz.
    - social: The social behavior factor of the Carviz.
    - hunger: The hunger level of the Carviz.
    """

    def __init__(self, energy=12, lifetime=12):
        """
        Initializes a new instance of the Carviz class.

        Args:
        - energy: The initial energy level of the Carviz. Default is 12.
        - lifetime: The maximum lifespan of the Carviz. Default is 12.
        """
        self.energy = energy
        self.age = 0
        self.lifetime = lifetime
        self.social = round(np.random.random(), 3)
        self.hunger = np.random.uniform(0, 1)


class Spot:
    """
    Represents a spot in the world that can contain animals and vegetation.

    Attributes:
    - VEGETOB: The Vegetob object representing vegetation in the spot.
    - ERBAST: The Erbast object present in the spot (None if absent).
    - CARVIZ: The Carviz object present in the spot (None if absent).
    - TYPE: The type of the spot (Water or Ground).
    - LIST_PRIDE: List of Carviz objects forming a pride in the spot.
    - LIST_HERD: List of Erbast objects forming a herd in the spot.
    """

    def __init__(self, VEGETOB=None, ERBAST=None, CARVIZ=None, LIST_PRIDE=None, LIST_HERD=None, TYPE=None):
        """
        Initializes a new instance of the Spot class.

        Args:
        - VEGETOB: The Vegetob object representing vegetation. Default is None.
        - ERBAST: The Erbast object present in the spot. Default is None.
        - CARVIZ: The Carviz object present in the spot. Default is None.
        - LIST_PRIDE: List of Carviz objects forming a pride. Default is an empty list.
        - LIST_HERD: List of Erbast objects forming a herd. Default is an empty list.
        - TYPE: The type of the spot (Water or Ground). Default is None.
        """
        self.VEGETOB = VEGETOB
        self.ERBAST = ERBAST
        self.CARVIZ = CARVIZ
        self.TYPE = TYPE
        self.LIST_PRIDE = LIST_PRIDE
        self.LIST_HERD = LIST_HERD

    def increase_vegetob(self):
        """
        Increases the vegetation density in the spot by 1, up to a maximum of 100.
        """
        self.VEGETOB.density = min(self.VEGETOB.density + 1, 100)

    def growing_erbast(self):
        """
        Handles the growth of Erbast animals in the spot and herd dynamics.
        """
        if self.ERBAST:
            self.ERBAST.age += 1
            if self.ERBAST.age >= self.ERBAST.lifetime:
                erbast_obj = self.ERBAST
                if erbast_obj.age == erbast_obj.lifetime:
                    sample = Erbast(energy=erbast_obj.energy / 2)
                    sample.social = erbast_obj.social
                    self.LIST_HERD += [sample] * 2
                    self.ERBAST = None
        for i, erbast in enumerate(self.LIST_HERD):
            erbast.age += 1
            if erbast.age >= erbast.lifetime:
                erbast_obj = erbast
                if erbast_obj.age == erbast_obj.lifetime:
                    sample = Erbast(energy=erbast_obj.energy / 2)
                    sample.social = erbast_obj.social
                    self.LIST_HERD += [sample] * 2
                    self.LIST_HERD.pop(i)
                    continue
            if self.VEGETOB.density > 30:
                increase_amount = self.VEGETOB.density * 0.05
                erbast.energy = min(erbast.energy + increase_amount, 100)
                self.VEGETOB.density -= increase_amount

    def growing_carviz(self):
        """
        Handles the growth of Carviz animals in the spot and pride dynamics.
        """
        if self.CARVIZ:
            self.CARVIZ.age += 1
            if self.CARVIZ.age >= self.CARVIZ.lifetime:
                carviz_obj = self.CARVIZ
                if carviz_obj.age == carviz_obj.lifetime:
                    sample = Carviz(energy=carviz_obj.energy / 2)
                    sample.social = carviz_obj.social
                    self.LIST_PRIDE += [sample] * 2
                    self.CARVIZ = None            
        for i, carviz in enumerate(self.LIST_PRIDE):
            carviz.age += 1
            if carviz.age >= carviz.lifetime:
                carviz_obj = carviz
                if carviz_obj.age == carviz_obj.lifetime:
                    sample = Carviz(energy=carviz_obj.energy / 2)
                    sample.social = carviz_obj.social
                    self.LIST_PRIDE += [sample] * 2
                    self.LIST_PRIDE.pop(i)

    def fight(self, incoming_carviz, existing_carviz):
        """
        Simulates a fight between two Carviz animals and returns the winner.

        Args:
        - incoming_carviz: The incoming Carviz animal.
        - existing_carviz: The existing Carviz animal.

        Returns:
        - The Carviz animal with higher energy (the winner).
        """
        return incoming_carviz if incoming_carviz.energy > existing_carviz.energy else existing_carviz

    def eat(self, carviz_animal, erbast_animal):
        """
        Simulates the consumption of an Erbast animal by a Carviz animal.

        Args:
        - carviz_animal: The Carviz animal.
        - erbast_animal: The Erbast animal.
        """
        carviz_animal.energy += 10
        self.LIST_HERD.remove(erbast_animal)

    def move(self, erbast_animal=None, carviz_animal=None):
        """
        Moves an Erbast or Carviz animal to another spot based on certain conditions.

        Args:
        - erbast_animal: The Erbast animal to move. Default is None.
        - carviz_animal: The Carviz animal to move. Default is None.
        """
        if erbast_animal:
            if not self.ERBAST:
                self.ERBAST = erbast_animal
            elif len(self.LIST_HERD) < 5:
                self.LIST_HERD.append(erbast_animal)

        if carviz_animal:
            if not self.CARVIZ and len(self.LIST_PRIDE) == 0:
                self.CARVIZ = carviz_animal
            elif self.CARVIZ:
                will_join_pride = np.random.choice(
                    [1, 0], p=[carviz_animal.social, 1 - carviz_animal.social])
                if will_join_pride and len(self.LIST_PRIDE) < 5:
                    self.LIST_PRIDE.append(carviz_animal)
                else:
                    self.CARVIZ = self.fight(carviz_animal, self.CARVIZ)
            if len(self.LIST_HERD) > 0:
                eat_chance = np.random.choice(
                    [1, 0], p=[carviz_animal.hunger, 1 - carviz_animal.hunger])
                if eat_chance == 1:
                    self.eat(carviz_animal, self.LIST_HERD[0])



def move(spot, spot2):
    """
    Moves objects between spots in the world.

    Args:
    - spot: The source spot to move from.
    - spot2: The destination spot to move to.
    """
    if (
        spot.ERBAST or
        spot.CARVIZ or
        spot.LIST_HERD or
        spot.LIST_PRIDE
    ):
        if spot.ERBAST:
            move_object(spot, spot2, spot.ERBAST, "ERBAST")

        if spot.CARVIZ:
            move_object(spot, spot2, spot.CARVIZ, "CARVIZ")

        if spot.LIST_HERD:
            move_list_objects(spot, spot2, spot.LIST_HERD, "LIST_HERD")

        if spot.LIST_PRIDE:
            move_list_objects(spot, spot2, spot.LIST_PRIDE, "LIST_PRIDE")


def move_object(spot, spot2, obj, name_obj):
    """
    Moves a single object (Erbast or Carviz) from one spot to another.

    Args:
    - spot: The source spot to move from.
    - spot2: The destination spot to move to.
    - obj: The object (Erbast or Carviz) to move.
    - name_obj: The name of the object (ERBAST or CARVIZ).
    """
    move_prop_obj = np.random.choice([1, 0], p=[1 - obj.social, obj.social])
    if move_prop_obj == 1 and spot2.TYPE == "Ground":
        obj.energy -= 1
        if obj.energy > 0:
            if name_obj == "ERBAST":
                spot2.move(obj, None)
            elif name_obj == "CARVIZ":
                spot2.move(None, obj)
        setattr(spot, name_obj, None)


def move_list_objects(spot, spot2, list_obj, name_obj):
    """
    Moves a list of objects (Erbast or Carviz) from one spot to another.

    Args:
    - spot: The source spot to move from.
    - spot2: The destination spot to move to.
    - list_obj: The list of objects (Erbast or Carviz) to move.
    - name_obj: The name of the objects (LIST_HERD or LIST_PRIDE).
    """
    for erbast_or_carviz in list_obj:
        move_prop_obj = np.random.choice(
            [1, 0], p=[1 - erbast_or_carviz.social, erbast_or_carviz.social])
        if move_prop_obj == 1 and spot2.TYPE == "Ground":
            erbast_or_carviz.energy -= 1
            if erbast_or_carviz.energy > 0:
                if name_obj == "LIST_PRIDE":
                    spot2.move(None, erbast_or_carviz)
                elif name_obj == "LIST_HERD":
                    spot2.move(erbast_or_carviz, None)


def create_spots(row_number, column):
    """
    Creates the initial grid of spots in the world.

    Args:
    - row_number: The number of rows in the grid.
    - column: The number of columns in the grid.

    Returns:
    - An array of spots representing the world grid.
    """
    spots = np.empty((row_number, column), dtype=object)
    type_choices = ['Water', 'Ground']
    erbast_prob = [0.8, 0.2]
    carviz_prob = [0.4, 0.6]

    for i, j in np.ndindex((row_number, column)):
        # Randomly choose the spot type based on the grid position
        spot_type = np.random.choice(
            type_choices,
            p=[1, 0] if i == 0 or j == 0 or i == row_number -
            1 or j == column - 1 else [0.2, 0.8]
        )

        if spot_type == 'Water':
            # Create a Water spot
            spots[i, j] = Spot(TYPE='Water')
        elif spot_type == 'Ground':
            # Randomly create an Erbast or leave it as None based on the probability
            erbast = np.random.choice([None, Erbast()], p=erbast_prob)
            # Randomly create a Carviz or leave it as None based on the probability
            carviz = np.random.choice([None, Carviz()], p=carviz_prob)
            # Create a Ground spot with Erbast, Carviz, and other attributes
            spots[i, j] = Spot(TYPE='Ground', ERBAST=erbast, CARVIZ=carviz, LIST_PRIDE=[
            ], LIST_HERD=[], VEGETOB=Vegetob())

    return spots


def simulate_day(spots):
    """
    Simulates a day in the world, including animal growth, movement, and interactions.

    Args:
    - spots: The array of spots representing the world grid.
    """
    for i in range(len(spots)):
        for j in range(len(spots[i])):
            spot = spots[i, j]
            if spot.TYPE == 'Ground':
                spot.increase_vegetob()
                spot.growing_erbast()
                spot.growing_carviz()

                max_density = spot.VEGETOB.density
                max_x, max_y = i, j

                # Define the eight possible neighboring directions around the current spot
                directions = [(i + dx, j + dy) for dx, dy in [(1, 0), (-1, 0),
                                                              (0, 1), (0, -1), (1, 1), (-1, -1), (1, -1), (-1, 1)]]

                for x, y in directions:
                    # Check if the neighboring position is within the grid boundaries and represents a ground spot
                    if 0 <= x < len(spots) and 0 <= y < len(spots[x]) and spots[x, y].TYPE == 'Ground':
                        # Compare the vegetation density of the neighboring spot with the current maximum density
                        if spots[x, y].VEGETOB.density > max_density:
                            # Update the maximum density and store the coordinates of the spot with the highest density
                            max_density = spots[x, y].VEGETOB.density
                            max_x, max_y = x, y

                # Check if a spot with higher density was found
                if max_density != -1:
                    # Move the current spot (and its animals) to the spot with the highest vegetation density
                    move(spot, spots[max_x, max_y])

                # Create a list of boolean values indicating if each neighboring spot has a vegetation density of 100
                neighbor_densities = [
                    spots[x, y].VEGETOB.density == 100
                    for x, y in directions
                    if 0 <= x < len(spots) and 0 <= y < len(spots[x]) and spots[x, y].TYPE == 'Ground'
                ]

                # Check if all neighboring spots have a density of 100
                if all(neighbor_densities):
                    # Remove the Erbast animals from the spot if all neighboring spots have maximum vegetation density
                    spot.ERBAST = None
                    spot.LIST_HERD = []


def display_world(spots, ax1, ax2):
    """
    Visualizes the current state of the world using matplotlib.

    Args:
    - spots: The array of spots representing the world grid.
    - ax1: The matplotlib axis for displaying the world grid.
    - ax2: The matplotlib axis for displaying the animal counts.
    """
    rows = len(spots)
    columns = len(spots[0])

    # Initialize an empty world with RGB values
    world = np.zeros((rows, columns, 3))

    for i in range(rows):
        for j in range(columns):
            spot = spots[i, j]
            if spot.TYPE == "Water":
                world[i, j] = [0, 0, 1]  # Blue color for water
            elif spot.TYPE == "Ground":
                if spot.ERBAST and spot.CARVIZ:
                    # Red color for both ERBAST and CARVIZ for Fighting
                    world[i, j] = [0.7, 0, 0]  # Adjusted red color (0.7, 0, 0)
                elif spot.ERBAST:
                    world[i, j] = [0.5, 0, 0.5]  # Purple color for ERBAST
                elif spot.CARVIZ:
                    world[i, j] = [0, 0, 0]  # Black color for CARVIZ
                else:
                    # Green color for default ground/vegetation
                    world[i, j] = [0, 0.5, 0]

    ax1.imshow(world)

    # Create a color bar legend
    colors = {
        'Water': 'blue',
        'Ground': 'green',
        'ERBAST': 'purple',
        'CARVIZ': 'black',
        'Fighting': 'red'  # Add "Fighting" to colors
    }
    labels = ['Water', 'Ground', 'ERBAST', 'CARVIZ', 'Fighting']  # Include "Fighting" label
    patches = [plt.Rectangle((0, 0), 1, 1, fc=colors[label])
               for label in labels]
    ax1.legend(patches, labels, loc='lower right')

    # Plot animal counts
    plot_animal_counts(spots, ax2)



# Plot the counts of animal types in the world
def plot_animal_counts(spots, ax):
    animals = ['ERBAST', 'CARVIZ']
    counts = [0, 0]

    for spot in spots.flatten():
        if spot.TYPE == 'Ground':
            if spot.ERBAST:
                counts[0] += 1
            if spot.CARVIZ:
                counts[1] += 1

    ax.barh(animals, counts)
    ax.set_title('Animal Counts')
    ax.set_xlabel('Count')
    ax.set_ylabel('Animal')

# Update the world simulation for each frame of the animation


def update_world(frame, spots, ax1, ax2, days_left_ax):
    simulate_day(spots)
    display_world(spots, ax1, ax2)
    days_left_ax.clear()  # Clear the axis
    days_left_ax.set_axis_off()  # Hide the axis
    days_left_ax.text(0.5, 0.95, f"Days Left: {days - frame}/{days}",
                      ha='center', va='top', fontsize=16)

# Animate the world simulation


def animate_world(spots, days):
    fig = plt.figure(figsize=(12, 12))
    gs = gridspec.GridSpec(3, 1, height_ratios=[3, 0.2, 1])

    ax1 = plt.subplot(gs[0])
    days_left_ax = plt.subplot(gs[1])
    ax2 = plt.subplot(gs[2])

    ax1.set_title("World Of Planisuss")
    ax2.set_title("Simulation Progress")
    days_left_ax.set_title("Days Left")

    # Set up the initial display
    display_world(spots, ax1, ax2)

    # Animation function
    def update(frame):
        update_world(frame, spots, ax1, ax2, days_left_ax)

    # Create the animation
    anim = animation.FuncAnimation(
        fig, update, frames=days, interval=500, repeat=False)

    plt.tight_layout()
    plt.show()


def validate_input(value, limit):
    if value > limit:
        print(f"Error: Input exceeds the limit of {limit}.")
        exit()


# Get user input for the number of rows
row_number = int(input("Enter the number of rows: "))
validate_input(row_number, 50)

# Get user input for the number of columns
column_number = int(input("Enter the number of columns: "))
validate_input(column_number, 50)

# Get user input for the number of days to simulate
days = int(input("Enter the number of days to simulate: "))
validate_input(days, 60)

# Create the spots
spots = create_spots(row_number, column_number)

# Animate the world simulation
animate_world(spots, days)
