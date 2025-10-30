import Board from '../components/Board.js';

const getBoard = async () => {
  let data = await fetch('http://localhost:5000/board', {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
    },
  });
  return data.json();
};

const showCell = async (cellId) => {
  const id = cellId.split('-');
  const i = parseInt(id[0]);
  const j = parseInt(id[1]);

  let data = await fetch('http://localhost:5000/board', {
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      i: i,
      j: j,
    }),
  });
  const container = document.getElementById('home-page');
  const currentBoard = container.querySelector('.board');
  if (currentBoard) {
    container.removeChild(currentBoard);
  }

  const newMatrix = await data.json();

  const win = newMatrix.data.win;
  const end = newMatrix.data.gameEnd;
  console.log({ win, end });
  if (end) {
    if (win) {
      alert('You Win!!');
    } else {
      alert('You Loss :c');
    }
  }

  const board = Board(newMatrix.data.board, showCell);
  container.appendChild(board);
};

function Game() {
  const container = document.createElement('div');
  container.id = 'home-page';
  
  const title = document.createElement('h1');
  title.innerText = 'Land Mines';
  title.classList = 'title';
  container.appendChild(title);

  // Botón de reinicio
  const restartButton = document.createElement('button');
  restartButton.innerText = 'Reiniciar Juego';
  restartButton.classList = 'restart-button';
  container.appendChild(restartButton);

  // Función para reiniciar el juego
  const restartGame = () => {
    console.log('Reiniciando juego...'); // Para debug
    
    const container = document.getElementById('home-page');
    const currentBoard = container.querySelector('.board');
    
    if (currentBoard) {
      container.removeChild(currentBoard);
      console.log('Tablero anterior eliminado'); // Para debug
    }
    
    // Obtener nuevo tablero
    getBoard().then((data) => {
      console.log('Nuevo tablero recibido:', data); // Para debug
      const board = Board(data.data.board, showCell);
      container.appendChild(board);
      console.log('Nuevo tablero agregado'); // Para debug
    }).catch((error) => {
      console.error('Error al obtener nuevo tablero:', error);
    });
  };

  // Agregar event listener - IMPORTANTE: después de definir restartGame
  restartButton.addEventListener('click', restartGame);

  // Inicializar el juego por primera vez
  getBoard().then((data) => {
    const board = Board(data.data.board, showCell);
    container.appendChild(board);
  });

  return container;
}

export default Game;
