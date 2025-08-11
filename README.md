## overview

15 puzzle game made with vanilla Javascript. It works both on computer and mobile devices. It features a move counter and timer, to know how fast you solved the puzzle. 

Ideally, you'd shuffle the tiles by clicking the shuffle button, which also starts the timer, and then start putting them back in the right order. But if you like chaos, you can also shuffle them yourself, and then put them back without the timer ever starting. It still works, but it will count also the moves you did to shuffle. I don't know why you might want to play the game like that, I'm just telling you that you *could*, technically, and no one would have the power to stop you. 

#### design

I looked at the [previous version of this project](https://tortaruga.github.io/vanilla-js-games/html/15-puzzle.html) and I was unsure whether to be depressed or grossed out, so I decided to be proactive instead and code a better one.
I had the idea to randomly generate a color for each tile, and I loved the way it looked, but since life is hard and doesn't handle anything to you for free this also meant that I broke the logic for everything else. 

###### the problem
The way the code worked before was by rendering the board with the tiles *every time* a move was made. This is because you need to update two "boards" everytime: the actual, visual board that the user sees, and the array representing the position of each tile. Updating the array, deleting the old board and creating a new one reflecting the new array was the easiest way to achieve it. 

But! Since everytime the tiles are created they get assigned a random color, this meant that they would change color with every move, threatening to trigger an epileptic seizure. I had to make a choice. I had to ask my heart and my head whether I would give up my pretty colors or seize the sword and slay the dragon.

###### the solution
Instead of deleting and creating boards as if they paid us for each new one, what we really needed was to switch the position of the tiles to reflect the update in the array.
So I gave each tile a position of absolute, and spent half an hour to figure out how to calculate the left and top coordinates for each one. 
After a few hardly intelligible scribbles on my notebook I found out that each tile position can be written as: 
```
tileElement.style.top = (Math.floor(index / boardWidth) * tileWidth) + 'px';
tileElement.style.left = ((index % boardWidth) * tileWidth) + 'px';
```

Wonderful! I was so elated! But I was celebrating victory too fast: the hardest part was still to come. 

Now, in addition to swapping the tiles in the array, I had to swap the corresponding divs. I spent some time trying an approach that was so foggy and convoluted that I don't think even I understood my own reasoning; it involved passing the selected tile index and the empty tile index as parameters to a function, which would then perform some weird logic to determine where the hell the selected tile had to move to, and then some other logic to move the empty tile, and then I would routinely forget to convert strings to numbers so that I would end up with my tile at position left: 1650px instead of 165px, not to mention the empty tile somehow moving always diagonally.

At that point I took a deep breath, reminded myself that violence wouldn't solve anything, deleted everything and started again.

It turns out there was no need for all that gymnastics I was doing.
Everything was solved beautifully by calculating the new position for each tile of the new array, which was quite straightforward because it's the same logic as positioning the tiles the first time: the calculation is based on the index, so by updating the index (and the data-index attribute) the tile gets positioned where it should, and the empty tile stops moving diagonally.  

```
function moveTile(index) {
    // switch tiles in the array
    [tiles[index], tiles[emptyIndex]] = [tiles[emptyIndex], tiles[index]]; 
       
    const selectedTile = document.querySelector(`.tile[data-index="${index}"]`);
    // switch the dataset index too
    selectedTile.dataset.index = emptyIndex; 
    document.querySelector('.empty').dataset.index = index;

    updateBoard();
}

function updateBoard() {
    // reflect the switch of the tiles array in the DOM tiles 
    tiles.forEach((tileValue, index) => {
        const tile = document.querySelector(`.tile[data-index="${index}"]`); 
        
        if (tile) {
          tile.style.top = (Math.floor(index / boardWidth) * tileWidth) + 'px';
          tile.style.left = ((index % boardWidth) * tileWidth) + 'px';
        }
    })
}
```

###### conclusion
Now the tiles have their random colors, the board doesn't get deleted and created fifty-seven billion times (which was not the best from a performance point of view...) and as a bonus I can also animate the tile to slide into its new position! Dragon successfully slain.