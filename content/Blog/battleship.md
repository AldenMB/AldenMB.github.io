Title: Return to Battleship
Date: Aug 28 2022
Category: Blog
Tags: Programming, Javascript
Summary: Reflecting on an old coding project, and what I would do differently now.
Slug: battleship

Particularly in grad school it can feel like your life is standing still. Because you are always learning new things, pushing yourself outside of your comfort zone, it can seem like you have not mastered anything. This is an illusion. To prove this, all you need to do is look at something you made before and feel the cringe. If you cringe at the way you solved a problem before, that is a sure sign that you know better now than you did then.

I am in the midst of refreshing my math department webpage, removing what is out-of-date and preserving what deserves to be preserved. Four years ago, I was teaching myself Javascript. I was also frequently playing Battleship over the phone with my then-girlfriend, now-wife. I made a quick tool to generate ship placements for me. last year I heard from an old friend that they had actually found the tool on a search, and that it had been useful. For that reason, I think it's worth preserving. I have copied it below.

I know better than to jump back in to a completed project. The tool I made works, and it works well. To redo it would be a wasted effort. However, the power of cringe is strong. I feel compelled to at least comment on the bad parts of the code, if nothing else that to reassure myself that I have indeed grown.

# The tool

This is a placement generator for Battleship. It will give you a randomly selected arrangement of your ships on the board. Here, "randomly selected" means that every possible arrangement is equally likely.

<form id="input_form">
Rows:<br> 
<input type="number" name="rows" max="99" min="1" value="10"><br>
Columns:<br>
<input type="number" name="columns" max="52" min="1" value="10"><br>
Ships:<br>
<textarea name="ships" rows="3" cols="17">
(C,5),(B,4),(c,3),(S,3),(D,2)
</textarea><br>
<input type="reset">
<button type="button" id="generate" onclick="generateBoard()">Generate!</button>
</form>

<pre id="the_board">
</pre>

Written by Alden Bradford on November 1-2, 2018. Generated placements are guaranteed to be (pseudo) random, but not guaranteed to be good. Use at your own risk.

<script>
"use strict";

var theForm = document.getElementById("input_form");

function generateBoard(){
	let board = new Board(theForm);
	let container = document.getElementById("the_board");
	board.placeShips();
	if (!board.failed){
		container.innerHTML = board.appearance();
	} else {
		container.innerHTML = "I tried 1,000,000 times, but \nI could not make the board work.";
	}	
}

function parseShips(string){
	let inputList = string.match(/\w,\d+/g);
	let ships = [];
	for(let i = 0; i < inputList.length; i++){
		let pair = [];
		pair[0] = inputList[i].match(/\w/)[0];
		pair[1] = parseInt(inputList[i].match(/\d+/)[0],10);
		ships[i] = pair;
	}
	return ships;
}

class Board {
	constructor(form){
		this.rows = form.elements.rows.valueAsNumber;
		this.cols = form.elements.columns.valueAsNumber;
		this.cells = [];
		this.initializeCells();
		this.shipPairs = parseShips(form.elements.ships.value);
		this.failed = false;
	}
	initializeCells(){
		this.cells = [];
	    for(let i=0;i<this.cols;i++){
			this.cells[i]=[];
			for(let j=0;j<this.rows;j++){
				this.cells[i][j]= new Cell();
			}
		}
	}
	appearance(){
		let string = 
		"   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z a b c d e f g h i j k l m n o p q r s t u v w x y z ";
		string = string.slice(0,this.cols*2+2);
		string = string.concat('\n');
		for(let j = 0; j < this.rows; j++){
			if( j < 9)  string = string.concat(' ');
			string = string.concat(`${j+1}`);
			for(let i = 0; i < this.cols; i++){
				string = string.concat(" "+this.cells[i][j].appearance());
			}
			string = string.concat('\n');
		}
		string = string.slice(0,-1);
		return string;
	}
	placeShip(charLenPair){
		let direction = ["v","h"][Math.floor(Math.random()*2)];
		let max_col = this.cols;
		let max_row = this.rows;
		if(direction === "v") max_row -= (charLenPair[1]-1);
		if(direction === "h") max_col -= (charLenPair[1]-1);
		if(max_row <1 || max_col<1) return false;
		let corner=[Math.floor(Math.random()*max_col),
					Math.floor(Math.random()*max_row)];
		let newShip = new Ship(charLenPair,direction,corner,this);
		if(newShip.canFit()){
			newShip.insert();
			return true;
		} else {
		return false;
		}
	}
	placeShips(){
		for(let attempt = 0; attempt < 1000000; attempt++){
			this.initializeCells();
			let attemptSuccessful = true;
			for(let i = 0; i<this.shipPairs.length; i++){
				if(!this.placeShip(this.shipPairs[i])){
					attemptSuccessful = false;
					break;
				}
			}
			if(attemptSuccessful){
				return;
			}
		}
		this.failed = true;
	}
}

class Ship{
	constructor(charLenPair,orientation,corner,board){
		this.appearance = charLenPair[0];
		this.length = charLenPair[1];
		this.orientation = orientation;
		this.corner = corner;
		this.board = board;
	}
	cells(){
		let cellList = [];
		if(this.orientation === "h"){
			for(let i = 0; i < this.length; i++){
				cellList[i] = this.board.cells[this.corner[0]+i][this.corner[1]  ];
			}
		}
		if(this.orientation === "v"){
			for(let i = 0; i < this.length; i++){
				cellList[i] = this.board.cells[this.corner[0]  ][this.corner[1]+i];
			}
		}
		return cellList;
	}
	canFit(){
		let cellList = this.cells();
		for(let i = 0; i<this.length; i++){
			if(cellList[i].contains) return false;
		}
		return true;
	}
	insert(){
		let cellList=this.cells();
		for(let i = 0; i<this.length; i++){
			cellList[i].contains = this;
		}	
	}
}

class Cell {
	constructor(){
	this.contains = null;
	}
	appearance(){
		return this.contains ? this.contains.appearance : '~';
	}
}

window.addEventListener('load', (event) => generateBoard());
</script>

# The good

One of the things which makes this particular tool so useful is that it is written in pure Javascript. Everyone with a modern web browser has a Javascript interpreter already installed and ready to go. The code is so portable that I was able to copy it directly here with no changes, and it still works perfectly. This is why I think Javascript is a good first language for many people to learn. There are exceptions, of course -- if you want to do data science you should learn Python, if you want to program Arduino you should learn C, et cetera. For most people, the benefit of having your code run anywhere outweighs the cost of having to deal with Javascript's peculiarities. Perhaps this will change soon, as there are several attempts in the works to make Python available in the browser. For now, Javascript has a strong advantage.

Let's have a look at my old code and see what nice qualities it has, lest we become overcritical.

```Javascript
"use strict";

var theForm = document.getElementById("input_form");

function generateBoard(){
	let board = new Board(theForm);
	let container = document.getElementById("the_board");
	board.placeShips();
	if (!board.failed){
		container.innerHTML = board.appearance();
	} else {
		container.innerHTML = "I tried 1,000,000 times, but \nI could not make the board work.";
	}	
}

function parseShips(string){
	let inputList = string.match(/\w,\d+/g);
	let ships = [];
	for(let i = 0; i < inputList.length; i++){
		let pair = [];
		pair[0] = inputList[i].match(/\w/)[0];
		pair[1] = parseInt(inputList[i].match(/\d+/)[0],10);
		ships[i] = pair;
	}
	return ships;
}

class Board {
	constructor(form){
		this.rows = form.elements.rows.valueAsNumber;
		this.cols = form.elements.columns.valueAsNumber;
		this.cells = [];
		this.initializeCells();
		this.shipPairs = parseShips(form.elements.ships.value);
		this.failed = false;
	}
	initializeCells(){
		this.cells = [];
	    for(let i=0;i<this.cols;i++){
			this.cells[i]=[];
			for(let j=0;j<this.rows;j++){
				this.cells[i][j]= new Cell();
			}
		}
	}
	appearance(){
		let string = 
		"   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z a b c d e f g h i j k l m n o p q r s t u v w x y z ";
		string = string.slice(0,this.cols*2+2);
		string = string.concat('\n');
		for(let j = 0; j < this.rows; j++){
			if( j < 9)  string = string.concat(' ');
			string = string.concat(`${j+1}`);
			for(let i = 0; i < this.cols; i++){
				string = string.concat(" "+this.cells[i][j].appearance());
			}
			string = string.concat('\n');
		}
		string = string.slice(0,-1);
		return string;
	}
	placeShip(charLenPair){
		let direction = ["v","h"][Math.floor(Math.random()*2)];
		let max_col = this.cols;
		let max_row = this.rows;
		if(direction === "v") max_row -= (charLenPair[1]-1);
		if(direction === "h") max_col -= (charLenPair[1]-1);
		if(max_row <1 || max_col<1) return false;
		let corner=[Math.floor(Math.random()*max_col),
					Math.floor(Math.random()*max_row)];
		let newShip = new Ship(charLenPair,direction,corner,this);
		if(newShip.canFit()){
			newShip.insert();
			return true;
		} else {
		return false;
		}
	}
	placeShips(){
		for(let attempt = 0; attempt < 1000000; attempt++){
			this.initializeCells();
			let attemptSuccessful = true;
			for(let i = 0; i<this.shipPairs.length; i++){
				if(!this.placeShip(this.shipPairs[i])){
					attemptSuccessful = false;
					break;
				}
			}
			if(attemptSuccessful){
				return;
			}
		}
		this.failed = true;
	}
}

class Ship{
	constructor(charLenPair,orientation,corner,board){
		this.appearance = charLenPair[0];
		this.length = charLenPair[1];
		this.orientation = orientation;
		this.corner = corner;
		this.board = board;
	}
	cells(){
		let cellList = [];
		if(this.orientation === "h"){
			for(let i = 0; i < this.length; i++){
				cellList[i] = this.board.cells[this.corner[0]+i][this.corner[1]  ];
			}
		}
		if(this.orientation === "v"){
			for(let i = 0; i < this.length; i++){
				cellList[i] = this.board.cells[this.corner[0]  ][this.corner[1]+i];
			}
		}
		return cellList;
	}
	canFit(){
		let cellList = this.cells();
		for(let i = 0; i<this.length; i++){
			if(cellList[i].contains) return false;
		}
		return true;
	}
	insert(){
		let cellList=this.cells();
		for(let i = 0; i<this.length; i++){
			cellList[i].contains = this;
		}	
	}
}

class Cell {
	constructor(){
	this.contains = null;
	}
	appearance(){
		return this.contains ? this.contains.appearance : '~';
	}
}
```

I like the functional and object-oriented qualities of this program. It makes sense to have a class for the game board and the ships, since there is a natural flow of these units going back and forth, asking each other questions. The game board is only generated in one spot, and a new object is made every time a board is generated. That makes it simple to follow the flow of the program and predict its state.

# The bad

This is all done as an inline script. That's not too unwieldy for a script of this size, but it is still not the way I would do it now. For one thing, it could lead to problems down the line if the program were to grow. For another, we are filling up the global namespace. This one is not too bad due to its size, only creating 6 global objects. Still, we could get it down to none by making this its own separate module.

While I like the object-oriented style, I do not like the use of Javascript classes here. For new projects I have adopted the "class free" style I learned from [Douglas Crockford's book](https://howjavascriptworks.com/). Essentially, we can avoid ever having to deal with `this` and all its oddities by avoiding the `class` syntax all together. There is some memory overhead in going class-free, since objects no longer share references to their methods, instead each storing their own methods. In practice this is not really a problem since we seldom have many instances of a class to begin with.

This brings me to a more fundamental problem with the code above. What's that class `Cell` supposed to be doing? All it does is store a reference to an object which is either `null` or a character. I suspect that in a previous version of the code I had the cell doing more work, keeping track of its neighbors or some such. With the wisdom of more years, I can see that the class should never have been made at all. I should have used the built-in strings instead.

# The janky

I don't wish to pick nits over strange formatting or missing semicolons. I won't harp on the liberal use of `let` where `const` would be more appropriate. Instead I will conclude by pointing out a few "What was I thinking?" absurdities.

Why oh why is there something called a `charLenPair` floating around? That should plainly just be an object, so that you can refer to the character or length as you see fit.

Why is there a function called `generateBoard` that lives outside the class `Board`? Surely this is the constructor for the class, and it should live within the class. I think I can tell what I though all those years ago, that I should separate those bits of code which interact with the DOM from those which keep to themselves. That's a good instinct, but it seems to have led to an absurd result in this case.

Why does the ship parser ignore parentheses? Why does it process each string three times, with three separate regular expressions? All of these can be explained by the fact that I was not experienced with regular expressions back then. That whole function feels wrong now, as a better-made regular expression would be more explicit and fail in more obvious ways.

Why do I left pad the digits in `Board.appearance` by checking if they are less than 9? That's much harder to read than `String.padStart`, for example. Of course, `String.padStart` was only introduced in 2017 after the famous `left-pad` scandal, so I may well not have heard of it yet at the time.

# Conclusion

Even though I can see many ways to improve it, this is ultimately a good piece of code. It does what it set out to do, and works just about anywhere.

It would be easy for me to hide my previous blunders, hastily rewrite the code here to be up to my current standards. I believe that would be a mistake. We should wear our early attempts with pride and learn from them where we can. This keeps us humble, and I hope it is instructive to those who are just beginning. It's okay to make mistakes, it's okay to do things in a poorly-thought-out way. It's better to make the mistakes and learn from them than to be paralyzed in unending revisions.