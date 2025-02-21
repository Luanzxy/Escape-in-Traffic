# Escape-in-Traffic



                 Primeiro Commit


import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()
screen = pygame.display.set_mode((800, 800))  # Tela de 800x800
clock = pygame.time.Clock()

# Cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)

# Fonte
pygame.font.init()
fonte = pygame.font.Font(None, 48)

# Função para desenhar um botão
def desenhar_botao(texto, x, y, largura, altura, cor_botao=PRETO):
    retangulo = pygame.Rect(x, y, largura, altura)
    pygame.draw.rect(screen, cor_botao, retangulo)
    texto_renderizado = fonte.render(texto, True, BRANCO)
    screen.blit(texto_renderizado, (x + (largura - texto_renderizado.get_width()) // 2,
                                     y + (altura - texto_renderizado.get_height()) // 2))

# Função para ler o recorde do arquivo
def ler_recorde():
    try:
        with open("recorde.txt", "r") as arquivo:
            return int(arquivo.read())
    except FileNotFoundError:
        return 0  
    except ValueError:
        return 0  

# Função para salvar o recorde no arquivo
def salvar_recorde(recorde):
    with open("recorde.txt", "w") as arquivo:
        arquivo.write(str(recorde))

# Função da tela inicial
def tela_inicial():
    botao_carros = pygame.Rect(0, 0, 0, 0)  
    botao_jogar = pygame.Rect(0, 0, 0, 0)

    recorde = ler_recorde()  

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:  
                    mouse_pos = pygame.mouse.get_pos()
                    if botao_carros.collidepoint(mouse_pos):
                        tela_carros()  
                    elif botao_jogar.collidepoint(mouse_pos):
                        jogo()  

        screen.fill(AZUL)    
        screen.fill(VERMELHO, rect=(400, 0, 400, 800))  

        titulo_renderizado = fonte.render("Escape in Traffic", True, VERDE)
        screen.blit(titulo_renderizado, (395 - titulo_renderizado.get_width() // 2, 50))

        largura_botao = 200   
        altura_botao = 75
        espaco_entre_botao = 20

        total_width = 2 * largura_botao + espaco_entre_botao
        start_x = (800 - total_width) // 2

        pos_x_carros = start_x
        pos_x_jogar = start_x + largura_botao + espaco_entre_botao

        # Botão "Carros" na esquerda
        botao_carros = pygame.Rect(pos_x_carros, 250, largura_botao, altura_botao)
        desenhar_botao("Carros", botao_carros.x, botao_carros.y, largura_botao, altura_botao)

        # Botão "Jogar" na direita
        botao_jogar = pygame.Rect(pos_x_jogar , 250 , largura_botao , altura_botao)
        desenhar_botao("Jogar", botao_jogar.x , botao_jogar.y , largura_botao , altura_botao)

        # Exibir o recorde na tela inicial como texto
        recorde_renderizado = fonte.render(f'Recorde: {recorde}', True, BRANCO)
        screen.blit(recorde_renderizado,(400 - recorde_renderizado.get_width() // 2 ,350))

        # Atualizar a tela
        pygame.display.flip()
        clock.tick(60)

# Função da tela de Recorde (removida já que agora é só visualização na tela inicial)
def tela_recorde():
    pass

# Função da tela de Carros
def tela_carros():
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:  
                    return

        screen.fill(BRANCO) 
        
        titulo_renderizado = fonte.render("Tela de Carros", True, PRETO)
        screen.blit(titulo_renderizado,(400 - titulo_renderizado.get_width() // 2 ,100))

        # Adicione mais elementos aqui conforme necessário

        pygame.display.flip()
        clock.tick(60)

# Função principal do jogo
def jogo():
    try:
        plano_de_fundo = pygame.image.load('estrada.jpg')
        plano_de_fundo = pygame.transform.scale(plano_de_fundo,(800 ,800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    try:
        imagem_original = pygame.image.load('carro_verde.png')
        imagem_original = imagem_original.convert_alpha()
    except pygame.error:
        print("Erro ao carregar a imagem do carro.")
        return

    largura_desejada = int(plano_de_fundo.get_width() * 0.15)   
    proporcao = largura_desejada / imagem_original.get_width()
    altura_desejada = int(imagem_original.get_height() * proporcao)

    imagem_alpha = pygame.transform.scale(imagem_original,(largura_desejada ,altura_desejada))
    
    x = (800 - largura_desejada) // 2   
    y = 600   
    velocidade = 5

    rodando = True
    
    outros_carros = []   
    pontuacao = 0

    while rodando:
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    rodando = False

        
        teclas = pygame.key.get_pressed()

        
        if teclas[pygame.K_LEFT] and x > 0:
            x -= velocidade

        
        if teclas[pygame.K_RIGHT] and x < screen.get_width() - largura_desejada:
            x += velocidade
        
       
       # Adicionar novos carros aleatoriamente à lista se houver espaço na tela.
    if len(outros_carros) < 5:   
           novo_carro_x = random.randint(0 ,screen.get_width() - largura_desejada)
           novo_carro_rect= imagem_alpha.get_rect(topleft=(novo_carro_x,-altura_desejada)) 
           outros_carros.append(novo_carro_rect)   
        
    screen.blit(plano_de_fundo,(0 ,0))
    screen.blit(imagem_alpha,(x,y))
       
    for carro in outros_carros[:]: 
           carro.y += velocidade /2   
           if carro.y > screen.get_height():   
               outros_carros.remove(carro)
               pontuacao +=1  
           else:
               screen.blit(imagem_alpha,(carro.x ,carro.y))   

               # Verifica colisão
               if carro.colliderect(pygame.Rect(x,y,width=largura_desejada,height=altura_desejada)):
                   print("Colisão! Você perdeu!")
                   rodando=False  

    pontuacao_renderizada=fonte.render(f'Pontuação: {pontuacao}',True)
    screen.blit(pontuacao_renderizada,(10 ,10))

       # Atualiza a tela
pygame.display.flip() 
clock.tick(60)

if __name__ == "__main__":
   tela_inicial() 













                         Segundo Commit








import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()
screen = pygame.display.set_mode((800, 800))  # Tela de 800x800
clock = pygame.time.Clock()

# Cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)

# Fonte
pygame.font.init()
fonte = pygame.font.Font(None, 48)

# Função para desenhar um botão
def desenhar_botao(texto, x, y, largura, altura, cor_botao=PRETO):
    retangulo = pygame.Rect(x, y, largura, altura)
    pygame.draw.rect(screen, cor_botao, retangulo)
    texto_renderizado = fonte.render(texto, True, BRANCO)
    screen.blit(texto_renderizado, (x + (largura - texto_renderizado.get_width()) // 2,
                                     y + (altura - texto_renderizado.get_height()) // 2))

# Função para ler o recorde do arquivo
def ler_recorde():
    try:
        with open("recorde.txt", "r") as arquivo:
            return int(arquivo.read())
    except FileNotFoundError:
        return 0  
    except ValueError:
        return 0  

# Função para salvar o recorde no arquivo
def salvar_recorde(recorde):
    with open("recorde.txt", "w") as arquivo:
        arquivo.write(str(recorde))

# Função da tela inicial
def tela_inicial():
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:  
                    mouse_pos = pygame.mouse.get_pos()
                    if botao_carros.collidepoint(mouse_pos):
                        tela_carros()
                    elif botao_jogar.collidepoint(mouse_pos):
                        return jogo()  # Sai da tela inicial e vai para o jogo

        screen.fill(AZUL)
        screen.fill(VERMELHO, rect=(400, 0, 400, 800))

        titulo_renderizado = fonte.render("Escape in Traffic", True, VERDE)
        screen.blit(titulo_renderizado, (395 - titulo_renderizado.get_width() // 2, 50))

        largura_botao = 200   
        altura_botao = 75
        espaco_entre_botao = 20

        total_width = 2 * largura_botao + espaco_entre_botao
        start_x = (800 - total_width) // 2

        pos_x_carros = start_x
        pos_x_jogar = start_x + largura_botao + espaco_entre_botao

        # Botão "Carros" na esquerda
        botao_carros = pygame.Rect(pos_x_carros, 250, largura_botao, altura_botao)
        desenhar_botao("Carros", botao_carros.x, botao_carros.y, largura_botao, altura_botao)

        # Botão "Jogar" na direita
        botao_jogar = pygame.Rect(pos_x_jogar , 250 , largura_botao , altura_botao)
        desenhar_botao("Jogar", botao_jogar.x , botao_jogar.y , largura_botao , altura_botao)

        recorde_renderizado = fonte.render(f'Recorde: {ler_recorde()}', True, BRANCO)
        screen.blit(recorde_renderizado,(400 - recorde_renderizado.get_width() // 2 ,350))

        pygame.display.flip()
        clock.tick(60)

        # Resto da lógica da tela inicial


# Função da tela de Recorde (removida já que agora é só visualização na tela inicial)
def tela_recorde():
    pass

# Função da tela de Carros
def tela_carros():
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:  
                    return

        screen.fill(BRANCO) 
        
        titulo_renderizado = fonte.render("Tela de Carros", True, PRETO)
        screen.blit(titulo_renderizado,(400 - titulo_renderizado.get_width() // 2 ,100))

        # Adicione mais elementos aqui conforme necessário

        pygame.display.flip()
        clock.tick(60)

carros_principais = ['imagens/carro_principal_verde.png','imagens/carro_principal_vermelho.png']

# Função principal do jogo
def jogo():
    try:
        plano_de_fundo = pygame.image.load('iamgens/estrada.jpg')
        plano_de_fundo = pygame.transform.scale(plano_de_fundo,(800 ,800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    try:
        imagem_original_verde = pygame.image.load('imagens/carro_verde.png').convert_alpha()
        imagem_original_vermleho = pygame.image.load('imagens/carro_principal_vermelho.png').convert_alpha()
        carros_pjogar = imagem_original_verde + imagem_original_vermleho
    except pygame.error:
        print("Erro ao carregar a imagem do carro.")
        return

    largura_do_carro = int(plano_de_fundo.get_width() * 0.15)   
    proporcao = largura_do_carro / carros_pjogar.get_width()
    altura_do_carro = int(carros_pjogar.get_height() * proporcao)

    imagem_alpha = pygame.transform.scale(carros_pjogar,(largura_do_carro ,altura_do_carro))
    
    x = (800 - largura_do_carro) // 2   
    y = 600   
    velocidade = 5

    rodando = True

    carros_segundarios_pbaixo = ['imagens/carro_segundario_azul_pbaixo.png','imagens/carro_segundario_branco_pbaixo.png']   
    carros_segundarios_pcima = ['imagens/carro_segundario_azul_pcima.png','imagens/carro_segundario_vermelho_pcima.png']
    carros_segundarios = carros_segundarios_pbaixo + carros_segundarios_pcima
    pontuacao = 0
     
    # Tempo que leva de um carro para outro 
    tempo_ultimo_carro = pygame.time.get_ticks()

    while rodando:
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    rodando = False

        
        teclas = pygame.key.get_pressed()

        
        if teclas[pygame.K_LEFT] and x > 0:
            x -= velocidade

        
        if teclas[pygame.K_RIGHT] and x < screen.get_width() - largura_do_carro:
            x += velocidade
        
       
       # Adicionar novos carros aleatoriamente de 5 em 5 segundos
        if len( carros_segundarios) < 5:
           novo_carro_x = random.randint(0 ,screen.get_width() - largura_do_carro)
           novo_carro_rect= imagem_alpha.get_rect(topleft=(novo_carro_x,-altura_do_carro)) 
           carros_segundarios.append(novo_carro_rect)
           tempo_ultimo_carro = pygame.time.get_tickts() # Faz com que reinicie o temporizador

        
    screen.blit(plano_de_fundo,(0 ,0))
    screen.blit(imagem_alpha,(x,y))
       
    for carro in  carros_segundarios[:]: 
           carro.y += velocidade /2   
           if carro.y > screen.get_height():   
                carros_segundarios.remove(carro)
                pontuacao +=1  
           else:
               screen.blit(imagem_alpha,(carro.x ,carro.y))   

               # Verifica colisão
               if carro.colliderect(pygame.Rect(x,y,width=largura_do_carro,height=altura_do_carro)):
                   print("Colisão! Você perdeu!")
                   rodando=False  

    pontuacao_renderizada=fonte.render(f'Pontuação: {pontuacao}',True, BRANCO)
    screen.blit(pontuacao_renderizada,(10 ,10))
       # Atualiza a tela
pygame.display.flip() 
clock.tick(60)

if __name__ == "__main__":
   tela_inicial()
















                     terceiro commit





import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()
screen = pygame.display.set_mode((800, 800))  # Tela de 800x800
clock = pygame.time.Clock()

# Cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)

# Fonte
fonte = pygame.font.Font(None, 48)

# Função para desenhar um botão
def desenhar_botao(texto, x, y, largura, altura, cor_botao=PRETO):
    retangulo = pygame.Rect(x, y, largura, altura)
    pygame.draw.rect(screen, cor_botao, retangulo)
    texto_renderizado = fonte.render(texto, True, BRANCO)
    screen.blit(texto_renderizado, (x + (largura - texto_renderizado.get_width()) // 2,
                                     y + (altura - texto_renderizado.get_height()) // 2))
    return retangulo

# Função para ler o recorde do arquivo
def ler_recorde():
    try:
        with open("recorde.txt", "r") as arquivo:
            return int(arquivo.read())
    except (FileNotFoundError, ValueError):
        return 0  

# Função para salvar o recorde no arquivo
def salvar_recorde(recorde):
    with open("recorde.txt", "w") as arquivo:
        arquivo.write(str(recorde))

# Tela inicial
def tela_inicial():
    try:
        plano_de_fundo_tela_inicial = pygame.image.load('Imagens/plano de fundo.jpg')
        plano_de_fundo_tela_inicial = pygame.transform.scale(plano_de_fundo_tela_inicial, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    largura_botao = 200
    altura_botao = 75
    espaco_entre_botao = 20

    total_width = 2 * largura_botao + espaco_entre_botao
    start_x = (800 - total_width) // 2

    botao_carros = desenhar_botao("Carros", start_x, 250, largura_botao, altura_botao)
    botao_jogar = desenhar_botao("Jogar", start_x + largura_botao + espaco_entre_botao, 250, largura_botao, altura_botao)

    while True:
        screen.blit(plano_de_fundo_tela_inicial, (0, 0))

        titulo_renderizado = fonte.render("Escape in Traffic", True, VERDE)
        screen.blit(titulo_renderizado, (400 - titulo_renderizado.get_width() // 2, 50))

        desenhar_botao("Carros", botao_carros.x, botao_carros.y, largura_botao, altura_botao)
        desenhar_botao("Jogar", botao_jogar.x, botao_jogar.y, largura_botao, altura_botao)

        recorde_renderizado = fonte.render(f'Recorde: {ler_recorde()}', True, BRANCO)
        screen.blit(recorde_renderizado, (400 - recorde_renderizado.get_width() // 2, 350))

        pygame.display.flip()
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:
                    mouse_pos = pygame.mouse.get_pos()
                    if botao_carros.collidepoint(mouse_pos):
                        tela_carros()
                    elif botao_jogar.collidepoint(mouse_pos):
                        jogo()

# Tela de carros
def tela_carros():
    try:
        plano_de_fundo_tela_carros = pygame.image.load('Imagens/plano de fundo.jpg')
        plano_de_fundo_tela_carros = pygame.transform.scale(plano_de_fundo_tela_carros, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    while True:
        screen.blit(plano_de_fundo_tela_carros, (0, 0))
        titulo_renderizado = fonte.render("Tela de Carros", True, PRETO)
        screen.blit(titulo_renderizado, (400 - titulo_renderizado.get_width() // 2, 100))

        pygame.display.flip()
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                return

# Função do jogo
def jogo():
    try:
        plano_de_fundo = pygame.image.load('imagens/estrada.jpg')
        plano_de_fundo = pygame.transform.scale(plano_de_fundo, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    try:
        carro_jogador = pygame.image.load('imagens/carro_principal_verde.png').convert_alpha()
    except pygame.error:
        print("Erro ao carregar a imagem do carro.")
        return

    largura_do_carro = 60
    altura_do_carro = 100
    carro_jogador = pygame.transform.scale(carro_jogador, (largura_do_carro, altura_do_carro))

    x, y = 370, 600
    velocidade = 5
    carros_inimigos = []
    tempo_ultimo_carro = pygame.time.get_ticks()
    pontuacao = 0
    rodando = True

    while rodando:
        screen.blit(plano_de_fundo, (0, 0))
        screen.blit(carro_jogador, (x, y))

        teclas = pygame.key.get_pressed()
        if teclas[pygame.K_LEFT] and x > 0:
            x -= velocidade
        if teclas[pygame.K_RIGHT] and x < screen.get_width() - largura_do_carro:
            x += velocidade

        if pygame.time.get_ticks() - tempo_ultimo_carro > 2000:
            carro_x = random.randint(0, screen.get_width() - largura_do_carro)
            carro_rect = pygame.Rect(carro_x, -altura_do_carro, largura_do_carro, altura_do_carro)
            carros_inimigos.append(carro_rect)
            tempo_ultimo_carro = pygame.time.get_ticks()

        for carro in carros_inimigos[:]:
            carro.y += velocidade / 2
            if carro.y > screen.get_height():
                carros_inimigos.remove(carro)
                pontuacao += 1
            elif carro.colliderect(pygame.Rect(x, y, largura_do_carro, altura_do_carro)):
                print("Colisão! Você perdeu!")
                rodando = False

            pygame.draw.rect(screen, VERMELHO, carro)

        pygame.display.flip()
        clock.tick(60)

tela_inicial()


