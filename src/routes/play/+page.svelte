 <script lang="ts">
    import { onMount } from "svelte";
   
    // 🎵 Configuração do Tema e Áudio
    type Theme = "default" | "lol" | "superheroes" | "anime";
    let selectedTheme: Theme = "default";
    let audio: HTMLAudioElement | null = null;
    
    // 🎵 Arquivos de Áudio para Cada Tema
    const audioFiles: Record<string, string> = {
    default: "/soundtrack default.mp3",
    lol: "/soundtrack lol.mp3",
    superheroes: "/soundtrack superheroes.mp3",
    anime: "/soundtrack anime.mp3",
  };
  
  // ▶️ Função para Tocar o Áudio do Tema Selecionado
  const playAudio = (theme: string) => {
    if (audio) {
      audio.pause(); 
      audio.currentTime = 0; 
    }

    // verifica o tema 
    audio = new Audio(audioFiles[theme]); //  *2521154
    audio.loop = true; // deixa a musica em loop 
    audio.play(); // inicia a musica 
  };
  // ⏸️ Função para Pausar o Áudio
  const pauseAudio = () => {
    if (audio) {
      audio.pause(); 
    }
  };



  //  Temas Disponíveis e suas Imagens
    const themes: Record<Theme, string[]> = {
        default: [
            "angryperson.jpg",
            "apple.jpg",
            "brain.jpg",
            "cat.jpg",
            "dog.jpg",
            "heart.jpg",
            "hotdog.jpg",
            "notebook.jpg",
            "pen.jpg",
            "remotecontrol.jpg",
        ],
        anime: [
            "Anime1.jpg",
            "Anime2.jpg",
            "Anime3.jpg",
            "Anime4.jpg",
            "Anime5.jpg",
            "Anime6.jpg",
            "Anime7.jpg",
            "Anime8.jpg",
            "Anime10.jpg",
            "Anime11.jpg",
        ],
        superheroes: [
            "batman.jpg",
            "captainamerica.jpg",
            "deadpool.jpg",
            "flash.jpg",
            "greenlantern.jpg",
            "ironman.jpg",
            "shazam.jpg",
            "spiderman.jpg",
            "superman.jpg",
            "thor.jpg",
        ],
        lol: [
            "ekko.jpg",
            "jinx.jpg",
            "kaisa.jpg",
            "leesin.jpg",
            "teemo.jpg",
            "vayne.jpg",
            "viktor.jpg",
            "volibear.jpg",
            "yasuo.jpg",
            "lucian.jpg",
        ],
    };

    // 🃏 Interface para Representar Cada Carta do Jogo
    interface Card {
        id: number;
        character: string;
        image: string;
        revealed: boolean;
        disabled: boolean;
        matched: boolean;
    }
   
    // 🗂️ Variáveis do Jogo
    let cards: Card[] = [];
    let firstCard: Card | null = null;
    let secondCard: Card | null = null;
    let timer: number = 0;
    let interval: ReturnType<typeof setInterval>;
    
    //🔀 Função para Embaralhar o Array de Cartas
    const shuffleArray = (array: any[]): any[] =>
        array.sort(() => Math.random() - 0.5);
    // 🎮 Função para Carregar o Jogo    
    const loadGame = (): void => {
        const duplicatedCharacters = [
            ...themes[selectedTheme],
            ...themes[selectedTheme],
        ];
        const shuffledCharacters = shuffleArray(duplicatedCharacters);

        cards = shuffledCharacters.map((image, index) => ({
            id: index,
            character: image,
            image: ` /${selectedTheme}/${image}`,
            revealed: false,
            disabled: false,
            matched: false,
        }));

        resetSelection();
        timer = 0;
        clearInterval(interval);
        startTimer();
    };
 
    // ⏱️ Função para Iniciar o Timer
    const startTimer = (): void => {
        clearInterval(interval);
        interval = setInterval(() => {
            timer++;
        }, 1000);
    };
    // ⌛ Função para Formatador de Tempo (mm:ss)
    const formatTime = (seconds: number): string => {
    const minutes = Math.floor(seconds / 60);
    const remainingSeconds = seconds % 60;
    return `${minutes}:${remainingSeconds < 10 ? "0" : ""}${remainingSeconds}`;
};
   //  Lógica do Jogo (Virar e Comparar Cartas)
    const handleClick = (card: Card): void => {
        if (card.revealed || card.disabled || (firstCard && secondCard)) return;

        if (!firstCard) {
            firstCard = { ...card, revealed: true };
            cards = cards.map((c) =>
                c.id === card.id ? { ...c, revealed: true } : c,
            );
        } else if (!secondCard) {
            secondCard = { ...card, revealed: true };
            cards = cards.map((c) =>
                c.id === card.id ? { ...c, revealed: true } : c,
            );
            checkCards();
        }
    };
   // Função para Verificar se as Cartas Combinam
    const checkCards = (): void => {
        if (firstCard && secondCard) {
            if (firstCard.character === secondCard.character) {
                cards = cards.map((card) =>
                    card.id === firstCard?.id || card.id === secondCard?.id
                        ? { ...card, disabled: true, matched: true }
                        : card,
                );
                console.log(
                    "Matched cards:",
                    cards.filter((c) => c.matched),
                );
                resetSelection();
                checkGameOver()
            } else {
                setTimeout(() => {
                    cards = cards.map((card) =>
                        card.id === firstCard?.id || card.id === secondCard?.id
                            ? { ...card, revealed: false }
                            : card,
                    );
                    resetSelection();
                }, 1000);
            }
        }
    };
     // Verificar se o jogo acabou
     let gameOver = false;
let finalTime = "";

// 🏆 Verificar se o jogo acabou e exibir mensagem animada
const checkGameOver = (): void => {
    const totalMatched = cards.filter((card) => card.matched).length;
    const totalCards = cards.length;

    if (totalMatched === totalCards) {
        clearInterval(interval);
        finalTime = formatTime(timer);
        gameOver = true;
    }
};

// 🔄 Reiniciar Jogo
const restartGame = (): void => {
    gameOver = false;
    loadGame();
};
    // 🔄 Função para Resetar a Seleção de Cartas
    const resetSelection = (): void => {
        firstCard = null;
        secondCard = null;
    };
    // 🚀 Inicializa o Jogo Quando a Página é Carregada
    onMount(() => {
        loadGame();
    });


    
</script>
git stash
{#if gameOver}
  <div id="game-over-modal">
      <div class="modal-content">
          <div class="celebration">
              <span class="emoji bounce">🎉</span>
              <span class="emoji bounce">🎊</span>
              <span class="congrats-text">Congratulations!</span>
              <span class="emoji bounce">🎊</span>
              <span class="emoji bounce">🎉</span>
          </div>

          <div class="final-time">
              <p>You finished the game in <strong>{finalTime}</strong>.</p>
          </div>

          <div class="modal-buttons">
              <button class="play-again" on:click={restartGame}>
                  🔄 Play Again
              </button>
              <button class="back-menu" on:click={() => (window.location.href = "/")}>
                  🏠 Back to Menu
              </button>
          </div>

          <div class="confetti-container">
              <span class="confetti">🎉🎉</span>
              <span class="confetti">🎉🎉</span>
              <span class="confetti">🎊🎊🎊</span>
              <span class="confetti">🎉🎉</span>
              <span class="confetti">🎉🎉</span>
              <span class="confetti">🎊🎊🎊</span>
              <span class="confetti">🎉🎉</span>
              <span class="confetti">🎉🎉</span>
              <span class="confetti">🎊🎊🎊</span>
          </div>
      </div>
  </div>
{/if}
<div id="play-logo-container">
    <header>
        <div id="logo-container">
            <div id="logo">
                <span>MEMORY MATCH</span>
                <img src="./brain.png" alt="Brain Icon" />
            </div>
        </div>
    </header>

    <aside>
        <main id="play-container">
            <div id="navigation-container">
                <div id="back-button">
                    <a  
                        href="/"
                        id="icon-back-button"
                        rel="nofollow"
                        aria-label="Button to return"
                        on:click={pauseAudio}
                        ><i
                            class="fa-solid fa-chevron-left fa-xl"
                            style="color: #ffffff;"
                        ></i></a
                    >
                </div>
                <div id="select-timer">
                    <div id="select-itens1">
                        <span>Theme: </span>
    
                        <label>
                            <select
                                id="select-themes"
                                bind:value={selectedTheme}
                                on:change={() => {
                                    loadGame();
                                    playAudio(selectedTheme)
                                }}
                            >
                                <option value="default">Default</option>
                                <option value="anime">Dragon Ball Z</option>
                                <option value="superheroes">Super Heroes</option>
                                <option value="lol">League of Legends</option>
                            </select>
                        </label>
                    </div>
    
                    <div id="select-itens2">
                        <span>Time: <span class="timer">{formatTime(timer)}</span></span>
                  </div>
            </div>
            </div>
              <div id="cards-container">
                {#each cards as card}
                    <button
                        class="memory-card"
                        class:revealed={card.revealed}
                        class:matched={card.matched}
                        on:click={() => handleClick(card)}
                        disabled={card.disabled}
                        aria-label={`Card ${card.character}`}
                    >
                        <div
                            class="front"
                            style="background-image: url({card.image}); background-size: cover;"
                        ></div>
                        <div class="back"></div>
                    </button>
                {/each}
            </div>
        </main>
    </aside>
</div>
