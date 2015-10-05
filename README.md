# Small-youtube
using Youtube API With HTML and JQUERY

// Search Handler
$(function() {
	// body...
	var searchField = $('#query');
	var icon = $('#searchBtn');
	var speed = 400;

	// Focus event handler
	$(searchField).on('focus', function(event) {
		$(this).animate({width:'100%'}, speed);
		$(icon).animate({right:'10px'}, speed);
	/* Act on the event */
	});

// Blur Event Handler
	$(searchField).on('blur', function() {
		if (searchField.val() == '') {
			$(searchField).animate({width:'45%'}, speed, function(){});
			$(icon).animate({right:'360px'}, speed, function(){});
		};
		/* Act on the event */
	});

	// submit form
	$('#searchForm').submit(function(e) {
		/* Act on the event */
		e.preventDefault();
	});

});

function search(){
	// clear results

	$('#results').html('');
	$('#buttons').html('');

	// Get form input
	q = $('#query').val();

	// Run GET Request on Api
	$.get("https://www.googleapis.com/youtube/v3/search",{
		part:'snippet, id',
		q: q,
		type:'video',
    
    // see other paramameters in this link: https://developers.google.com/youtube/v3/docs/search/list
		// change a key with your own key
		
		key:'AIzaSyA3W0M1ZIBtwQ9WvxH9Nh8rqLtkN7uIwlc'},
		function(data){
			var nextPageToken = data.nextPageToken;
			var prevPageToken = data.prevPageToken;

			//Log data
			console.log(data)
			$.each(data.items, function(i, item){

			//Get output
			var output = getOutput(item);

			// Display results
			$('#results').append(output);

		});

			var buttons = getButtons(prevPageToken,nextPageToken);
			// Display Buttons
			$('#buttons').append(buttons);
	});
};


function Page(btn){
	var token = $(btn).data('token');
	var q = $(btn).data('query');

	// clear results
	$('#results').html('');
	$('#buttons').html('');

	// Get form input
	q = $('#query').val();

	// Run GET Request on Api
	// 
	// Other request method we can send to make search in youtube is available at this link
	// https://developers.google.com/youtube/v3/docs/search/list
	
	$.get("https://www.googleapis.com/youtube/v3/search",{
		part:'snippet, id',
		q: q,
		pageToken: token,
		type:'video',
		key:'AIzaSyA3W0M1ZIBtwQ9WvxH9Nh8rqLtkN7uIwlc'}, 
		function(data){
			var nextPageToken = data.nextPageToken;
			var prevPageToken = data.prevPageToken;

			//Log data
			console.log(data)
			$.each(data.items, function(i, item){

			//Get output
			var output = getOutput(item);

			// Display results
			$('#results').append(output);

		});

			var buttons = getButtons(prevPageToken,nextPageToken);
			// Display Buttons
			$('#buttons').append(buttons);
	});
}


function getOutput(item){
	var videoId = item.id.videoId;
	var title = item.snippet.title;
	var description = item.snippet.description;
	var thumb = item.snippet.thumbnails.high.url;
	var channelTitle = item.snippet.channelTitle;
	var videoDate = item.snippet.publishedAt;

	// build output string
	var output = '<li>'+ 
	'<div class="listLeft">'+
	'<img src="'+thumb+'">'+
	'</div>'+
	'<div class="listRight">'+
	'<h3><a class="fancybox fancybox.iframe" href="http://www.youtube.com/embed/'+videoId+'">'+title+'</h3>'+
	'<small> By<span class="cTitle"> '+channelTitle+'</span> on'+videoDate+'</small>'+
	'<p>'+description+'</p>'+
	'</div>'+
	'</li>'+
	'<div class="clearfix"></div>' + '';

	return output;
}

// Build the buttons
function getButtons(prevPageToken,nextPageToken){
	if(!prevPageToken){
		var btnOutput = '<div class="buttonContainer">'+
		'<button id="nextButton" class="pagingButton" data-token="'+nextPageToken+'" data-query="'+q+'" onclick=Page("#nextButton");>Next Page</button></div>'; 
	} else {
		var btnOutput = '<div class="buttonContainer">'+
		'<button id="prevButton" class="pagingButton" data-token="'+prevPageToken+'" data-query="'+q+'" onclick=Page("#prevButton");>Prev Page</button>'+
		'<button id="nextButton" class="pagingButton" data-token="'+nextPageToken+'" data-query="'+q+'" onclick=Page("#nextButton");>Next Page</button></div>'; 
	}
	return btnOutput;
}
