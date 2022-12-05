Title: Orrell's Quick Wall Paper Calculator
Date: December 5 2022
Category: Blog
Tags: Calculators, Slide rules
Summary: A trade show freebie which is almost a slide rule
Slug: oqwpc

My wife got me an early holiday gift this year. It's an ancient trade show promotional item, which must be nearly 100 years old by now. I believe it was made in 1923 or 1924, for reasons which will soon become obvious. In addition to working as an advertisement for L.C. Orrell & Company, it serves as a rough area estimation tool for wall paper.

It consists of a cardboard envelope with two holes cut into it, along with a card which slides inside the envolope. The whole thing measures 5.5 by 2.75 inches. To use it, you slide the card in and out, and different figures show through the holes.

<script type="text/javascript">
  function makeDraggable(evt) {
	var svg = evt.target;

	svg.addEventListener('mousedown', startDrag);
	svg.addEventListener('mousemove', drag);
	svg.addEventListener('mouseup', endDrag);
	svg.addEventListener('mouseleave', endDrag);
	svg.addEventListener('touchstart', startDrag);
	svg.addEventListener('touchmove', drag);
	svg.addEventListener('touchend', endDrag);
	svg.addEventListener('touchleave', endDrag);
	svg.addEventListener('touchcancel', endDrag);

	var selectedElement, offset, transform;

	function getMousePosition(evt) {
	  var CTM = svg.getScreenCTM();
	  if (evt.touches) { evt = evt.touches[0]; }
	  return {
		x: (evt.clientX - CTM.e) / CTM.a,
		y: (evt.clientY - CTM.f) / CTM.d
	  };
	}

	function startDrag(evt) {
		selectedElement = document.getElementById("slide");
		offset = getMousePosition(evt);

		// Make sure the first transform on the element is a translate transform
		var transforms = selectedElement.transform.baseVal;

		if (transforms.length === 0 || transforms.getItem(0).type !== SVGTransform.SVG_TRANSFORM_TRANSLATE) {
		  // Create an transform that translates by (0, 0)
		  var translate = svg.createSVGTransform();
		  translate.setTranslate(0, 0);
		  selectedElement.transform.baseVal.insertItemBefore(translate, 0);
		}

		// Get initial translation
		transform = transforms.getItem(0);
		offset.x -= transform.matrix.e;
		offset.y -= transform.matrix.f;

	}

	function drag(evt) {

	  evt.preventDefault();
	  if (selectedElement) {

		var coord = getMousePosition(evt);
		var dx = coord.x - offset.x;
		
		dx = Math.max(dx, 0)

		transform.setTranslate(dx, 0);
	  }
	}

	function endDrag(evt) {
	  selectedElement = false;
	}
  }
</script>

<svg xmlns="http://www.w3.org/2000/svg"
	 viewBox="0 0 4000 1732"
	 onload="makeDraggable(evt)"
	 cursor="grab">
<image x="40" y="10" id="slide" height="1715" width="3325" xlink:href="/front_slide.png" />
<image xlink:href="/front.png" />
</svg>

I have put the image above into an SVG to let you drag the card in and out on your screen by clicking and dragging. Try it out! Thanks to [Peter Collingridge](https://www.petercollingridge.co.uk/tutorials/svg/interactive/dragging/) for the dragging code. If you would like the bare images, here is the [envolope]({attach}front.png) and the 
[card]({attach}front_slide.png).

On the reverse is a simple table which shows the area of a rectangular ceiling in various dimensions, in case you want to wallpaper your ceiling for some reason.

![a table of celing areas]({attach}back.jpeg)

On the back of the inserted card is a calendar, hence my claim that this was made in 1923 or 1924.

![a 1924 calendar]({attach}back_slide.jpeg)

I have not found out much about the company that made this, except for a digitized version of [one of their sample catalogs](https://commons.wikimedia.org/wiki/File:Sample_Book,_L.C._Orrell_and_Co.,_Book_No._2,_1906_(CH_18802803-71).jpg).

# How it works

Mostly the mechanism is fairly straightforward. The area of the sides of a room is simply

$$
2(\text{length}+\text{width})\times\text{height}.
$$

Each position of the slide corresponds to one sum, $\text{length}+\text{width}$. The table of heights just shows the corresponding sum, multiplied by the corresponding height. In fact, the sum itself shows up as well (albeit rounded up) as the "yards of border" entry in the table.

# The mystery

Most of the card is quite self-explanatory. There is one thing which still perplexes me though. The calculator claims to give "exact quantities required for over 800 different sized rooms". Where did this number come from? The width is marked out from 7 to 20 giving us 14 widths, and the length is marked from 8 to 20 giving us 13 lengths. Put together with the six given heights, we get $14\times 13\times 6 = 1092$ dimensions. Wouldn't "over 1000 different sized rooms" have sounded much better? Perhaps they are being more careful than that. After all, an 8-by-10 room is no different from a 10-by-8 room, so maybe they don't want to count those twice. In that case, we would find $\binom{13}{2}+13=91$ different floor dimensions (choosing two numbers from 8 to 20, or choosing a 7 and a number from 8 to 20). Multiplying by the six heights leaves us 546, woefully short of the promised 800. 

Perhaps they initially intended to make the whole thing a bit bigger, giving all the dimensions listed on the table on the back? No, this gives us the same problem. Treating length and width as separate things, we get 1122 room sizes, much larger than 800. Treating length and width as interchangeable gets us 726 room sizes, still falling short of 800.

If you have any idea where 800 comes from, let me know. I am stumped!