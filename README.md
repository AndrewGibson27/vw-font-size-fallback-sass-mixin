# Sass mixin: vw font sizing with ems as fallback
I like sizing headings with the viewport width unit, but older browsers don't recognize it. Here's one solution.

Inspired by this [article](http://sassbreak.com/viewport-relative-headings-with-sass/) from Sass Break, which outlines the `$vw-context/$vw-base` method.

## The code
```sass
@mixin vw-with-fallback($font-sizes, $vw-base) {
	$vw-context: (1280*.01) * 1px;

	@each $list in $font-sizes {
		$i: index($font-sizes, $list);
		$currem: nth($list, 2);
		$currbp: nth($list, 1);
		$usevw: nth($list, 3);

		@if $i == 1 {
			@media screen and (min-width: #{$currbp}) {
				font-size: $currem;
				font-size: ($vw-base/$vw-context) * 1vw;
			}
		}

		@else {
			@if $usevw == 'use-vw' {
				@media screen and (max-width: #{$currbp}) {
					font-size: $currem;
					font-size: ($vw-base/$vw-context) * 1vw;
				}
			}
			@else {
				@media screen and (max-width: #{$currbp}) {
					font-size: $currem;
				}
			}
		}
	}
}
```

## Usage
```sass
$hed-font-sizes:
	1281px 10em 'use-vw',
	1280px 8em 'use-vw',
	767px 6em 'use-vw',
	400px 5em 'no-vw';

h1 {
    @include vw-with-fallback($hed-font-sizes, 100px);
}
```

### In $hed-font-sizes:
+ The first value per line is the breakpoint at which the font size takes effect. **Note**: The first breakpoint the mixin receives is used as a `min-width` media query. The others are `max-width` media queries.
+ The second value is the backup font size (in ems) that will take effect at that breakpoint
+ The third value should either be **use-vw** or **no-vw**. Use the latter if you want to use the em value instead of the vw value at a particular breakpoint.

### In the @include
+ The first argument is the list, the contents of which is explained above
+ The second argument is used to calculate vw. Sending 100px to the mixin means that, at a window width of 1280px (the $vw-context), the h1 should have a font size of 100px. You can make the $vw-context whatever value you want. (I encourage you to read the Sass Break [article](http://sassbreak.com/viewport-relative-headings-with-sass/) about this.)

## Test it
+ Download the zip file
+ Install grunt and grunt-contrib-sass: `npm install --save-dev`
+ `grunt sass`
+ Open /example/index.html

## License
MIT
