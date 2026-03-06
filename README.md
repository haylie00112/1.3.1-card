# 1.3.1-card
import turtle as trtl
import random as rand
import math

# set up screen
wn = trtl.Screen()
wn.setup(width=800, height=800)
wn.bgcolor("black")
wn.tracer(0)

# ground turtle
ground = trtl.Turtle()
ground.hideturtle()
ground.speed(0)
ground.color("#032403")
ground.penup()
ground.goto(-400, -350)
ground.pendown()
ground.pensize(150)
ground.forward(800)

# message turtle
message = trtl.Turtle()
message.hideturtle()
message.penup()
message.color("white")
message.goto(0, 300)

# launcher turtles
firework_1 = trtl.Turtle()
firework_1.hideturtle()
firework_1.penup()
firework_1.goto(-250, -275)
firework_1.color("red")

firework_2 = trtl.Turtle()
firework_2.hideturtle()
firework_2.penup()
firework_2.goto(0, -275)
firework_2.color("yellow")

firework_3 = trtl.Turtle()
firework_3.hideturtle()
firework_3.penup()
firework_3.goto(250, -275)
firework_3.color("cyan")

# disappear turtle used to erase trails
disappear = trtl.Turtle()
disappear.hideturtle()
disappear.penup()
disappear.color("black")
disappear.pensize(8)
disappear.goto(-250, -275)

# functions 

def explode(x, y, color="white", count=40, speed=8):
    particles = []
    for _ in range(count):
        p = trtl.Turtle(shape="circle")
        p.hideturtle()
        p.speed(0)
        p.penup()
        p.color(color)
        p.shapesize(0.4, 0.4)
        p.goto(x, y)
        p.showturtle()
        angle = rand.uniform(0, 360)
        rad = math.radians(angle)
        vx = math.cos(rad) * rand.uniform(speed * 0.5, speed)
        vy = math.sin(rad) * rand.uniform(speed * 0.5, speed)
        life = rand.randint(20, 35)
        particles.append({"t": p, "vx": vx, "vy": vy, "life": life})

    def update():
        any_alive = False
        for part in particles:
            if part["life"] > 0:
                any_alive = True
                tx, ty = part["t"].position()
                part["t"].goto(tx + part["vx"], ty + part["vy"])
                part["vy"] -= 0.35  # gravity
                part["life"] -= 1
                # shrink to simulate fading
                s = max(0.05, part["t"].shapesize()[0] - 0.02)
                part["t"].shapesize(s, s)
                if part["life"] <= 0:
                    part["t"].hideturtle()
        if any_alive:
            wn.update()
            wn.ontimer(update, 30)

    update()

def erase_trail(x_start, distance):
    # draw a black line over the launched trail to "erase" it
    disappear.penup()
    disappear.goto(x_start, -275)
    disappear.setheading(90)
    disappear.pendown()
    disappear.pensize(6)
    disappear.forward(distance)
    disappear.penup()
    wn.update()

def launch_1():
    firework_1.goto(-250, -275)
    firework_1.pendown()
    firework_1.pensize(4)
    firework_1.setheading(90)
    firework_1.forward(230)
    firework_1.penup()
    firework_1.goto(-250, 40)
    # show the trail immediately
    wn.update()
    # explode after a short delay
    wn.ontimer(lambda: explode(-250, 40, "red", count=48, speed=9), 250)
    # erase the trail after the explosion
    wn.ontimer(lambda: erase_trail(-250, 230), 900)
    # hide launcher head after done
    wn.ontimer(lambda: firework_1.hideturtle(), 1000)

def launch_2():
    firework_2.goto(0, -275)
    firework_2.pendown()
    firework_2.pensize(4)
    firework_2.setheading(90)
    firework_2.forward(150)
    firework_2.penup()
    firework_2.goto(0, 10)
    wn.update()
    wn.ontimer(lambda: explode(0, 10, "yellow", count=48, speed=9), 250)
    wn.ontimer(lambda: erase_trail(0, 150), 900)
    wn.ontimer(lambda: firework_2.hideturtle(), 1000)

def launch_3():
    firework_3.goto(250, -275)
    firework_3.pendown()
    firework_3.pensize(4)
    firework_3.setheading(90)
    firework_3.forward(230)
    firework_3.penup()
    firework_3.goto(250, 40)
    wn.update()
    wn.ontimer(lambda: explode(250, 40, "cyan", count=48, speed=9), 250)
    wn.ontimer(lambda: erase_trail(250, 230), 900)
    wn.ontimer(lambda: firework_3.hideturtle(), 1000)

def launch_all():
    launch_1()
    wn.ontimer(launch_2, 800)
    wn.ontimer(launch_3, 1600)

# key bidnings 
wn.onkeypress(launch_all, "space")
wn.listen()
# display message and start
message.write("You're braver than you believe, stronger than you know, and smarter than you think!", align="center", font=("Arial", 11, "normal"))
launch_all()
message.goto(0, 270)
message.write("Press space to re-launch!", align="center", font=("Arial", 11, "normal"))  
wn.mainloop()
