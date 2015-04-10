### Gulpfile ###
When using SASS, this gulpfile will do all of the following:
+ autoprefix
+ compress
+ livereload
+ sourcemap

##### LiveReload note
Make sure you have the Chrome plugin installed and enabled, which you can find here
[LiveReload](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)

I came across some issues trying to get autoprefixing, compression, sourcemaps that work in the Chrome inspector, and livereload of the CSS without reloading the whole page. I saw others were having similar problems so I figured I would document it here.

This is the simplest example:
``` javascript
'use strict';

var gulp = 			require('gulp'),
	sass = 			require('gulp-ruby-sass'),
	autoprefixer = 	require('gulp-autoprefixer'),
	concat = 		require('gulp-concat'),
	sourcemaps = 	require('gulp-sourcemaps'),
	livereload = 	require('gulp-livereload');

// set directory locations
var srcDir = 			'_src/styles',
	cssDestination = 	'_css';

// Styles Task which we'll call when there's a change in one of our SASS files
// This compiles SASS to CSS, adds autoprefixing, writes to a sourcemap, and compiles to a master.css file
gulp.task('styles', function() {
	return sass (
		srcDir + '/', {
			sourcemap: true,
			style: 'compressed'
		}
	)
	.pipe(concat('master.css'))
	.pipe(autoprefixer())
	.pipe(sourcemaps.write('../' + cssDestination +'/',{ 
		sourceRoot: srcDir,
		includeContent: false  		// default is true, which includes the entire css in the sourcemap
	}))
	.pipe(gulp.dest(cssDestination));
});

gulp.task('watch', function () {
	// when any SASS file changes, run the Styles task
	gulp.watch(srcDir + '/*.scss', ['styles']);

	// start listener for livereload function
	livereload.listen();
	
	// watch for changes only in our master.css file
	// this is separate from the 'styles' task since we want to make sure we run it synchronously
	gulp.watch( cssDestination + '/master.css', function(event) {
		// only reload the resource that has changed, in this case, our master.css file. this allows livereload without a page refresh
		livereload.changed(event.path); 
	});

});

// add both of our tasks to our default task which starts them both
gulp.task('default', ['styles', 'watch']);
```

##### Processing images
To process images, run `gulp images` when you're ready as it will not process them by default

Since processing images with gulp:
+ takes a long time
+ gulp.watch doesn't watch for new or deleted files
+ there's a gulp-watch package to add aforementioned functionality but we want to keep Fabric to the bare minimum
+ you can still process images when you want to by running `gulp images`

Since no two projects are the same, this gulpfile moves all the directory variables out of the code to make it easier to set up your own project.

This gulpfile is part of the open source Fabric project which you can [follow here](https://github.com/flickerbox/fabric)

##### The actual Gulpfile

``` javascript
// very important to put /*# sourceMappingURL=master.css.map */ as the first line of the master.scss

'use strict';

var gulp = 			require('gulp'),
	uglify = 		require('gulp-uglify'),
	sass = 			require('gulp-ruby-sass'),
	autoprefixer = 	require('gulp-autoprefixer'),
	plumber = 		require('gulp-plumber'),
	notify = 		require('gulp-notify'),
	concat = 		require('gulp-concat'),
	sourcemaps = 	require('gulp-sourcemaps'),
	livereload = 	require('gulp-livereload'),
	imagemin = 		require('gulp-imagemin'),
	jpegoptim = 	require('imagemin-jpegoptim'),
	pngquant = 		require('imagemin-pngquant');

var srcDir = 			'_src',
	jsSource = 			'scripts',
	jsDestination = 	'_js',
	cssSource = 		'styles',
	cssDestination = 	'_css',
	imageSource = 		'_src/media',
	imageDestination = 	'_media';

// this is the error shown using plumber and notify:
var onError = function(err) {
	notify.onError({
		title:    "Gulp Error",
		message:  "<%= error.message %>",
	})(err);

	this.emit('end');
};

// Uglifies a.k.a. minifies JS
gulp.task('scripts', function() {
	gulp.src(srcDir + '/' + jsSource + '/*.js')
		.pipe(plumber({errorHandler: onError}))
		.pipe(uglify())
		.pipe(gulp.dest(jsDestination))
		.pipe(livereload()); // run livereload on js changes
});

// Styles Task
gulp.task('styles', function() {
	return sass (
		srcDir + '/' + cssSource + '/', {
			sourcemap: true,
			noCache: false, 	// default = false. saves ~10% runtime but stores in a .sass-cache folder.
			style: 'compressed'
		}
	)
	.on('error', onError)
	.pipe(concat('master.css'))
	.pipe(autoprefixer())
	.pipe(sourcemaps.write('../' + cssDestination +'/',{ 
		sourceRoot: srcDir + '/' + cssSource,
		includeContent: false  // default is true, which includes the entire css in the sourcemap
	}))
	.pipe(gulp.dest(cssDestination));
});

// Image compression is off by default but is included. You can access by running `gulp images` 
// or add it to the `default` gulp task at the bottom.

gulp.task('images', function() {
  return gulp.src(imageSource+'/**/*.{png,jpg,jpeg}')
  	.pipe(jpegoptim({
  		max: 70
  	})())
  	.pipe(pngquant({
  		quality: 	'65-80',
  		speed: 		4})())
    .pipe(gulp.dest(imageDestination));
});

gulp.task('watch', function () {
	
	gulp.watch(srcDir + '/' + jsSource + '/**/*.js', ['scripts']);
	gulp.watch(srcDir + '/' + cssSource + '/*.scss', ['styles']);
	livereload.listen();
	gulp.watch(['**/*.html','**/*.php', cssDestination + '/master.css' ], function(event) {
		livereload.changed(event.path); 
	});

});


gulp.task('default', ['scripts', 'styles', 'watch']);
```
##### The package.json
``` javascript
{
  "name": "fabric",
  "version": "1.0.0",
  "description": "Fabric is a very opinionated, yet very basic framework Flickerbox, Inc. websites.",
  "main": "gulpfile.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/flickerbox/fabric.git"
  },
  "author": "Flickerbox, Inc.",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/flickerbox/fabric/issues"
  },
  "homepage": "https://github.com/flickerbox/fabric",
  "devDependencies": {
    "gulp": "^3.8.11",
    "gulp-autoprefixer": "^2.1.0",
    "gulp-concat": "^2.5.2",
    "gulp-livereload": "^3.8.0",
    "gulp-notify": "^2.2.0",
    "gulp-plumber": "^1.0.0",
    "gulp-ruby-sass": "^1.0.1",
    "gulp-sourcemaps": "^1.5.1",
    "gulp-uglify": "^1.1.0",
    "gulp-imagemin":"^2.2.1",
    "imagemin-jpegoptim":"^4.0.0",
    "imagemin-pngquant" : "^4.0.0"
  }
}
```

If you're interested in keeping up to date with this project, you can [follow it here](https://github.com/flickerbox/fabric)
