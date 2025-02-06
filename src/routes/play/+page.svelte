 <script lang="ts">
    import { onMount } from "svelte";
   // tema e o audio
    type Theme = "default" | "lol" | "superheroes" | "anime";
    let selectedTheme: Theme = "default";
    let audio: HTMLAudioElement | null = null;
    
// os audios de cada tema
    const audioFiles: Record<string, string> = {
    default: "/soundtrack default.mp3",
    lol: "/soundtrack lol.mp3",
    superheroes: "/soundtrack superheroes.mp3",
    anime: "/soundtrack anime.mp3",
  };
  
  // tocar a musica dependendo do tema
  const playAudio = (theme: string) => {
    if (audio) {
      audio.pause(); 
      audio.currentTime = 0; 
    }

    // verifica o tema 
    audio = new Audio(audioFiles[theme]);
    audio.loop = true; // deixa a musica em loop eterno
    audio.play(); // inicia a musica 
  };
  // pausa o a musica
  const pauseAudio = () => {
    if (audio) {
      audio.pause(); 
    }
  };




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

    interface Card {
        id: number;
        character: string;
        image: string;
        revealed: boolean;
        disabled: boolean;
        matched: boolean;
    }
   // declarando as variaveis
    let cards: Card[] = [];
    let firstCard: Card | null = null;
    let secondCard: Card | null = null;
    let timer: number = 0;
    let interval: ReturnType<typeof setInterval>;
   // deixar o array aleatorio 
    const shuffleArray = (array: any[]): any[] =>
        array.sort(() => Math.random() - 0.5);

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
 // inicia o tempo
    const startTimer = (): void => {
        clearInterval(interval);
        interval = setInterval(() => {
            timer++;
        }, 1000);
    };
    // converter o tempo em segundos para minuto 0:00
    const formatTime = (seconds: number): string => {
    const minutes = Math.floor(seconds / 60);
    const remainingSeconds = seconds % 60;
    return `${minutes}:${remainingSeconds < 10 ? "0" : ""}${remainingSeconds}`;
};
   // verifica as cartas viradas e desabilita elas caso estejam erradas
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
 // Verifica se a carta deu match com a outra
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
    const checkGameOver = (): void => {
        const totalMatched = cards.filter((card) => card.matched).length;
        const totalCards = cards.length;

        if (totalMatched === totalCards) {
            clearInterval(interval);
            let playAgain;
            do {
                playAgain = prompt(
                    `Congratulations! You finished the game in ${formatTime(timer)}. Would you like to play again? (Type "yes" to continue or "no" to quit)`
                );

                if (playAgain === null) {
                    alert("Thank you for playing! See you next time!");
                    return;
                }

                playAgain = playAgain.trim().toLowerCase();

                if (playAgain !== "yes" && playAgain !== "no") {
                    alert(
                        'Invalid response! Please type "yes" to play again or "no" to quit.',
                    );
                }
            } while (playAgain !== "yes" && playAgain !== "no");

            if (playAgain === "yes") {
                loadGame();
            } else if (playAgain === "no") {
                window.location.href = "/";
            }
        }
    };

    const resetSelection = (): void => {
        firstCard = null;
        secondCard = null;
    };

    onMount(() => {
        loadGame();
    });


    
</script>

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
