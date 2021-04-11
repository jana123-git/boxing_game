# boxing_game
a boxing game created by Sounak Jana with pygame
game_name = 'Boxing Game'  
if game_name == 'Boxing Game':
    import pygame as pg
    import random
    import sys

    pg.init()
    pg.font.init()
    joysticks=[]
    for i in range(pg.joystick.get_count()):
        joysticks.append(pg.joystick.Joystick(i))
        joysticks[-1].init()

    SCREENWIDTH = 500
    SCREENHEIGHT = 500

    SCREEN = pg.display.set_mode((SCREENWIDTH, SCREENHEIGHT))
    pg.display.set_caption('Boxing Game')
    pg.display.set_icon(pg.image.load('front_pic2.png').convert_alpha())

    front_pic = pg.image.load('front_pic.png').convert_alpha()
    punch_sound = pg.mixer.Sound('PUNCH.wav')
    ringbell = pg.mixer.Sound('BoxingBell.wav')

    FPS = 40

    CLOCK = pg.time.Clock()

    white = (255, 255, 255)
    black = (0, 0, 0)
    skin = (236, 222, 159)
    brown = (100, 70, 40) 
    blue = (0, 0, 255)
    red = (255, 0, 0)
    RINGX = 80
    RINGY = 90
    RINGSIZE = 340

    def welcome():
        while True:
            for event in pg.event.get():
                if event.type == pg.QUIT:
                    pg.quit()
                    sys.exit()
                if event.type == pg.JOYBUTTONDOWN:
                    if event.button == 0:
                        pg.quit()
                        sys.exit() 
                    if event.button == 5 :
                        return 
                if event.type == pg.KEYDOWN:
                    if event.key == pg.K_ESCAPE:
                        pg.quit()
                        sys.exit()
                    if event.key == pg.K_KP_ENTER or event.key == pg.K_RETURN:
                        return
                if event.type == pg.MOUSEBUTTONDOWN:
                    return

            SCREEN.fill(white) 
            drawing_ring()
            showing_text(55, 'Boxing Game', black, 100, 100)
            showing_text(35, ' Press enter to play', black, 100, 220)
            SCREEN.blit(front_pic, [150, 260])

            pg.display.update()
            CLOCK.tick(FPS)

    def game():
        headsize = 75
        handsize = 70
        armsize = 40
        handwidth = 15
        glvsize = 40

        headminx = RINGX + (handwidth + armsize )
        headmaxx = RINGX + RINGSIZE - (handwidth + armsize + headsize) 

        """player1"""#player1 is computer
        #player1 head
        head1x = headminx
        head1y = RINGY + 50
        #player1 right hand
        p1_r_armx = head1x + headsize-handwidth
        p1_r_army = head1y + (headsize+handwidth)/2
        p1_r_handx = head1x + headsize + armsize
        p1_r_handy = head1y + headsize
        #player1 left hand
        p1_l_armx = head1x + handwidth
        p1_l_army = head1y + (headsize+handwidth)/2
        p1_l_handx = head1x - armsize
        p1_l_handy = head1y + headsize
        #player1 right hand gloves
        p1_r_glvx = p1_r_handx - glvsize/2
        p1_r_glvy = p1_r_handy - glvsize/2
        #player1 left hand gloves
        p1_l_glvx = p1_l_handx - glvsize/2
        p1_l_glvy = p1_l_handy - glvsize/2

        """player2"""#player2 is me
        #player2 head
        head2x = headmaxx
        head2y = head1y +60 + headsize 
        #player2 right hand
        p2_r_armx = head2x + headsize-handwidth
        p2_r_army = head2y + (headsize-handwidth)/2
        p2_r_handx = head2x + headsize + armsize
        p2_r_handy = head2y
        #player2 left hand
        p2_l_armx = head2x + handwidth
        p2_l_army = head2y + (headsize-handwidth)/2
        p2_l_handx = head2x - armsize
        p2_l_handy = head2y
        #player2 right hand gloves
        p2_r_glvx = p2_r_handx - glvsize/2
        p2_r_glvy = p2_r_handy - glvsize/2
        #player2 left hand gloves
        p2_l_glvx = p2_l_handx - glvsize/2
        p2_l_glvy = p2_l_handy - glvsize/2

        p1_r_punch = False
        p1_l_punch = False

        p2_r_punch = False
        p2_l_punch = False

        p1_vel = 10
        p2_vel = 10
        punch_vel = 5

        move_directions = ['right', 'left']

        p1_move_direction = 'none'
        p2_move_direction = 'none'

        punch =['right_hand', 'left_hand']
        p1_punch = 'none'

        p1_punch_time_gap = 500 #in milisec
        p1_move_direction_time_gap = 200 #in milisec 
        game_time1 = 60 #in sec
        game_time2 = 5 #in sec

        p1_punch_selection = pg.USEREVENT + 1
        p1_move_direction_selection = pg.USEREVENT + 2
        timer_set = pg.USEREVENT + 3

        pg.time.set_timer(p1_punch_selection, p1_punch_time_gap)
        pg.time.set_timer(p1_move_direction_selection, p1_move_direction_time_gap)
        pg.time.set_timer(timer_set, 1000)

        comp_score = 0
        my_score = 0
        
        keep_alive = True
        while keep_alive:
            for event in pg.event.get():
                if event.type == pg.QUIT:
                    pg.quit()
                    sys.exit()
                if event.type == pg.JOYBUTTONDOWN:
                    if event.button == 0:
                        pg.quit()
                        sys.exit() 
                    if event.button == 5 :
                        keep_alive = False 
                if event.type == pg.KEYDOWN:
                    if event.key == pg.K_ESCAPE:
                        pg.quit()
                        sys.exit()
                    if event.key == pg.K_KP_ENTER or event.key == pg.K_RETURN:
                        keep_alive = False 
                if event.type == pg.MOUSEBUTTONDOWN:
                    keep_alive = False

            SCREEN.fill(white) 
            drawing_ring()
            drawing_hand(p1_r_handx, p1_r_handy, p1_l_handx, p1_l_handy, p1_r_armx, p1_r_army, p1_l_armx, p1_l_army, p2_r_handx, p2_r_handy, p2_l_handx, p2_l_handy, p2_r_armx, p2_r_army, p2_l_armx, p2_l_army, handsize, handwidth, armsize)
            drawing_gloves(p1_r_glvx, p1_r_glvy, p1_l_glvx, p1_l_glvy,p2_r_glvx, p2_r_glvy, p2_l_glvx, p2_l_glvy, glvsize)
            drawing_head(head1x, head1y, head2x, head2y, headsize)
            
            showing_text(35, ' Press enter to start', black, 100, 220)

            pg.display.update()
            CLOCK.tick(FPS)

        while True:
            for event in pg.event.get():
                if event.type == pg.QUIT:
                    pg.quit()
                    sys.exit()
                if event.type == pg.JOYBUTTONDOWN:
                    if event.button == 0:
                        pg.quit()
                        sys.exit()
                if event.type == pg.KEYDOWN:
                    if event.key == pg.K_ESCAPE:
                        pg.quit()
                        sys.exit()
                if event.type == timer_set:
                    game_time2 -= 1
                
            SCREEN.fill(white)
            drawing_ring()
            drawing_hand(p1_r_handx, p1_r_handy, p1_l_handx, p1_l_handy, p1_r_armx, p1_r_army, p1_l_armx, p1_l_army, p2_r_handx, p2_r_handy, p2_l_handx, p2_l_handy, p2_r_armx, p2_r_army, p2_l_armx, p2_l_army, handsize, handwidth, armsize)
            drawing_gloves(p1_r_glvx, p1_r_glvy, p1_l_glvx, p1_l_glvy,p2_r_glvx, p2_r_glvy, p2_l_glvx, p2_l_glvy, glvsize)
            drawing_head(head1x, head1y, head2x, head2y, headsize)
            
            if game_time2 == 5:
                showing_text(70, 'Ready', black, 160, 195)
            if game_time2 == 4:
                showing_text(70, '3', black, 235, 195)
            if game_time2 == 3:
                showing_text(70, '2', black, 235, 195)
            if game_time2 == 2:
                showing_text(70, '1', black, 240, 195)
            if game_time2 == 1:
                showing_text(70, 'Start', black, 175, 195)
            if game_time2 == 0:
                ringbell.play()
                break

            pg.display.update()
            CLOCK.tick(FPS)

        while True:
            for event in pg.event.get():
                if event.type == pg.QUIT:
                    pg.quit()
                    sys.exit()
                if event.type == pg.JOYBUTTONDOWN:
                    if event.button == 0:
                        pg.quit()
                        sys.exit()
                    if event.button == 1 :
                        p2_move_direction = 'right'
                    if event.button == 2 :
                        p2_move_direction = 'left'
                    if event.button == 3 and p2_r_handy >= head2y and p2_l_punch == False :
                        p2_r_punch = True
                    if event.button == 4 and p2_r_handy >= head2y and p2_l_punch == False :
                        p2_l_punch = True
                if event.type == pg.KEYDOWN:
                    if event.key == pg.K_ESCAPE:
                        pg.quit()
                        sys.exit()
                    if event.key == pg.K_RIGHT :
                        p2_move_direction = 'right'
                    if event.key == pg.K_LEFT :
                        p2_move_direction = 'left'
                    if event.key == pg.K_r and p2_r_handy >= head2y and p2_l_punch == False :
                        p2_r_punch = True
                    if event.key == pg.K_l and p2_r_handy >= head2y and p2_l_punch == False :
                        p2_l_punch = True
                if event.type == pg.MOUSEBUTTONDOWN :
                    if event.button == 1 and p2_l_handy >= head2y and p2_r_punch == False :#1 is for left click
                        p2_l_punch = True
                    if event.button == 3 and p2_r_handy >= head2y and p2_l_punch == False :#3 is for right click
                        p2_r_punch = True 
                if event.type == p1_punch_selection :
                    p1_punch = random.choice(punch)
                    if p1_punch == 'right_hand': 
                        p1_r_punch = True
                    if p1_punch == 'left_hand':
                        p1_l_punch = True 
                if event.type == p1_move_direction_selection :
                    p1_move_direction = random.choice(move_directions)
                if event.type == timer_set:
                    game_time1 -=  1

            if game_time1 == 0:
                ringbell.play()
                return my_score, comp_score

            if p2_move_direction == 'right' and head2x < headmaxx:
                head2x += p2_vel
                p2_r_armx += p2_vel
                p2_l_armx += p2_vel
                p2_r_handx += p2_vel
                p2_l_handx += p2_vel
                p2_r_glvx += p2_vel
                p2_l_glvx += p2_vel
                p2_move_direction = 'none'
            if p2_move_direction == 'left' and head2x > headminx:
                head2x -= p2_vel
                p2_r_armx -= p2_vel
                p2_l_armx -= p2_vel
                p2_r_handx -= p2_vel
                p2_l_handx -= p2_vel
                p2_r_glvx -= p2_vel
                p2_l_glvx -= p2_vel
                p2_move_direction = 'none'
                    
            if p1_move_direction == 'right' and head1x  < headmaxx:
                head1x += p1_vel
                p1_r_armx += p1_vel
                p1_l_armx += p1_vel
                p1_r_handx += p1_vel
                p1_l_handx += p1_vel
                p1_r_glvx += p1_vel
                p1_l_glvx += p1_vel
                p1_move_direction = 'none'
            if p1_move_direction == 'left' and head1x > headminx:
                head1x -= p1_vel
                p1_r_armx -= p1_vel
                p1_l_armx -= p1_vel
                p1_r_handx -= p1_vel
                p1_l_handx -= p1_vel
                p1_r_glvx -= p1_vel
                p1_l_glvx -= p1_vel
                p1_move_direction = 'none'

            if p2_r_punch == True :
                if p2_r_glvy >= head2y - 70:
                    p2_r_handy -= punch_vel
                    p2_r_glvy -= punch_vel
                if p2_r_glvy < head2y - 70:
                    p2_r_handy += punch_vel
                    p2_r_glvy += punch_vel
                    p2_r_punch = False
            if p2_r_punch == False :
                if p2_r_handy <= head2y:
                    p2_r_handy += punch_vel
                    p2_r_glvy += punch_vel
            if p2_l_punch == True :
                if p2_l_glvy >= head2y - 70:
                    p2_l_handy -= punch_vel
                    p2_l_glvy -= punch_vel
                if p2_l_glvy < head2y - 70:
                    p2_l_handy += punch_vel
                    p2_l_glvy += punch_vel
                    p2_l_punch = False
            if p2_l_punch == False :
                if p2_l_handy <= head2y:
                    p2_l_handy += punch_vel
                    p2_l_glvy += punch_vel
            if p1_r_punch == True :
                if p1_r_glvy + glvsize<= head2y + 10:
                    p1_r_handy += punch_vel
                    p1_r_glvy += punch_vel
                if p1_r_glvy + glvsize > head2y + 10:
                    p1_r_handy -= punch_vel
                    p1_r_glvy -= punch_vel
                    p1_r_punch = False
            if p1_r_punch == False :
                if p1_r_handy >= head1y + headsize:
                    p1_r_handy -= punch_vel
                    p1_r_glvy -= punch_vel
            if p1_l_punch == True :
                if p1_l_glvy + glvsize <= head2y + 10:
                    p1_l_handy += punch_vel
                    p1_l_glvy += punch_vel
                if p1_l_glvy + glvsize > head2y + 10:
                    p1_l_handy -= punch_vel
                    p1_l_glvy -= punch_vel
                    p1_l_punch = False
            if p1_l_punch == False :
                if p1_l_handy >= head1y + headsize:
                    p1_l_handy -= punch_vel
                    p1_l_glvy -= punch_vel
            
            if p1_r_glvy + glvsize >= head2y+10 and p1_r_glvx >= head2x and p1_r_glvx + glvsize<= head2x + headsize:
                punch_sound.play()
                comp_score += 1
            if p1_l_glvy + glvsize >= head2y+10 and p1_l_glvx >= head2x and p1_l_glvx + glvsize <= head2x + headsize:
                punch_sound.play()
                comp_score += 1
            if p2_r_glvy <= head2y - 70 and p2_r_glvx >= head1x and p2_r_glvx + glvsize<= head1x + headsize:
                punch_sound.play()
                my_score += 1
            if p2_l_glvy <= head2y - 70 and p2_l_glvx >= head1x and p2_l_glvx + glvsize<= head1x + headsize:
                punch_sound.play()
                my_score += 1

            SCREEN.fill(white)
            drawing_ring()
            drawing_hand(p1_r_handx, p1_r_handy, p1_l_handx, p1_l_handy, p1_r_armx, p1_r_army, p1_l_armx, p1_l_army, p2_r_handx, p2_r_handy, p2_l_handx, p2_l_handy, p2_r_armx, p2_r_army, p2_l_armx, p2_l_army, handsize, handwidth, armsize)
            drawing_gloves(p1_r_glvx, p1_r_glvy, p1_l_glvx, p1_l_glvy,p2_r_glvx, p2_r_glvy, p2_l_glvx, p2_l_glvy, glvsize)
            drawing_head(head1x, head1y, head2x, head2y, headsize)
            
            showing_text(30, ('Time left : '+ sec_to_min(game_time1)), black, 250, 20)
            showing_text(30, ('Your Score : '+str(my_score)), black, 100, 380)
            showing_text(30, ("Computer's Score : "+str(comp_score)), black, 100, 80)

            pg.display.update()
            CLOCK.tick(FPS)

    def game_over():
        your_score , comp_score = game()
        while True:
            for event in pg.event.get():
                if event.type == pg.QUIT:
                    pg.quit()
                    sys.exit()
                if event.type == pg.JOYBUTTONDOWN:
                    if event.button == 0:
                        pg.quit()
                        sys.exit() 
                    if event.button == 5 :
                        return 
                if event.type == pg.KEYDOWN:
                    if event.key == pg.K_ESCAPE:
                        pg.quit()
                        sys.exit()
                    if event.key == pg.K_KP_ENTER or event.key == pg.K_RETURN:
                        return
                if event.type == pg.MOUSEBUTTONDOWN:
                    return

            SCREEN.fill(white) 
            drawing_ring()

            if your_score > comp_score:
                showing_text(50, 'You Win', black, 170, 220)
            elif your_score < comp_score:
                showing_text(50, 'Computer Win', black, 100, 220)
            else:
                showing_text(50, 'Draw', black, 200, 220)

            showing_text(25, 'Press enter to play again', black, 120, 300)
            
            pg.display.update()
            CLOCK.tick(FPS)

    def sec_to_min(second):
        minute = second//60
        second = second%60
        if second < 10 :
            time = '0'+(str(minute)+':0'+str(second))
        else:
            time = '0'+(str(minute)+':'+str(second))
        return time
    def showing_text(font_size, font_text, font_color, fontx, fonty):
        font = pg.font.SysFont('Harrington', font_size,False, True)
        text = font.render(font_text, True, font_color)
        SCREEN.blit(text,[fontx, fonty])

    def drawing_ring():
        pg.draw.rect(SCREEN, black, [RINGX-25, RINGY-25, RINGSIZE+51, RINGSIZE+51])
        pg.draw.rect(SCREEN, white, [RINGX-15, RINGY-15, RINGSIZE+30, RINGSIZE+30])
        pg.draw.line(SCREEN, red, (RINGX-20,RINGY-20), (0, 0), 10)
        pg.draw.line(SCREEN, black, (0, SCREENHEIGHT), (RINGX-20, RINGY+RINGSIZE+20), 10)
        pg.draw.line(SCREEN, black, (RINGX+RINGSIZE+20, RINGY-20), (SCREENWIDTH, 0), 10)
        pg.draw.line(SCREEN, blue, (SCREENWIDTH, SCREENHEIGHT), (RINGX+RINGSIZE+20, RINGY+RINGSIZE+20), 10)
        pg.draw.polygon(SCREEN, red, [(RINGX-25,RINGY-25), (RINGX-25, RINGY+15), (RINGX+15, RINGY-25)])
        pg.draw.polygon(SCREEN, black, [(RINGX+RINGSIZE+25,RINGY-25), (RINGX+RINGSIZE+25, RINGY+15), (RINGX+RINGSIZE-15, RINGY-25)])
        pg.draw.polygon(SCREEN, black, [(RINGX-25,RINGY+RINGSIZE+25), (RINGX-25, RINGY+RINGSIZE-15), (RINGX+15, RINGY+RINGSIZE+25)])
        pg.draw.polygon(SCREEN, blue, [(RINGX+RINGSIZE+25,RINGY+RINGSIZE+25), (RINGX+RINGSIZE+25, RINGY+RINGSIZE-15), (RINGX+RINGSIZE-15, RINGY+RINGSIZE+25)])
    def drawing_hand(p1_r_handx, p1_r_handy, p1_l_handx, p1_l_handy, p1_r_armx, p1_r_army, p1_l_armx, p1_l_army, p2_r_handx, p2_r_handy, p2_l_handx, p2_l_handy, p2_r_armx, p2_r_army, p2_l_armx, p2_l_army, handsize, handwidth, armsize):
        #p1 right hand
        pg.draw.circle(SCREEN, skin, (p1_r_handx, (p1_r_handy - handsize)), handwidth/2)
        pg.draw.line(SCREEN, skin, (p1_r_handx, p1_r_handy), (p1_r_handx, (p1_r_handy - handsize)), handwidth)
        pg.draw.line(SCREEN, skin, (p1_r_armx, p1_r_army), (p1_r_handx , (p1_r_handy - handsize)), handwidth)
        #p1 left hand 
        pg.draw.circle(SCREEN, skin, (p1_l_handx, (p1_l_handy - handsize)), handwidth/2)
        pg.draw.line(SCREEN, skin, (p1_l_handx, p1_l_handy), (p1_l_handx, (p1_l_handy - handsize)), handwidth)
        pg.draw.line(SCREEN, skin, (p1_l_armx, p1_l_army), (p1_l_handx , (p1_l_handy - handsize)), handwidth)
        #p2 right hand
        pg.draw.circle(SCREEN, brown, (p2_r_handx, (p2_r_handy + handsize)), handwidth/2)
        pg.draw.line(SCREEN, brown, (p2_r_handx, p2_r_handy), (p2_r_handx, (p2_r_handy + handsize)), handwidth)
        pg.draw.line(SCREEN, brown, (p2_r_armx, p2_r_army), (p2_r_handx , (p2_r_handy + handsize)), handwidth)
        #p2 left hand
        pg.draw.circle(SCREEN, brown, (p2_l_handx, (p2_l_handy + handsize)), handwidth/2)
        pg.draw.line(SCREEN, brown, (p2_l_handx, p2_l_handy), (p2_l_handx, (p2_l_handy + handsize)), handwidth)
        pg.draw.line(SCREEN, brown, (p2_l_armx, p2_l_army), (p2_l_handx , (p2_l_handy + handsize)), handwidth)
    def drawing_gloves(p1_r_glvx, p1_r_glvy, p1_l_glvx, p1_l_glvy,p2_r_glvx, p2_r_glvy, p2_l_glvx, p2_l_glvy, glvsize):
        #p1 gloves 
        pg.draw.circle(SCREEN, red, ((p1_r_glvx + glvsize/2), (p1_r_glvy + glvsize/2)), glvsize/2)
        pg.draw.circle(SCREEN, red, ((p1_l_glvx + glvsize/2), (p1_l_glvy + glvsize/2)), glvsize/2)
        #p2 gloves
        pg.draw.circle(SCREEN, blue, ((p2_r_glvx + glvsize/2), (p2_r_glvy + glvsize/2)), glvsize/2)
        pg.draw.circle(SCREEN, blue, ((p2_l_glvx + glvsize/2), (p2_l_glvy + glvsize/2)), glvsize/2)
    def drawing_head(head1x, head1y, head2x, head2y, headsize):
        #p1 head
        pg.draw.circle(SCREEN, skin, (head1x+headsize/2,head1y+headsize/2), headsize/2)
        #p2 head
        pg.draw.circle(SCREEN, brown, (head2x+headsize/2,head2y+headsize/2), headsize/2)

    welcome()
    while True:
        game_over()
