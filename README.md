# Sass mixin: vw font sizing with fallback
I like sizing headings with the viewport width unit, but older browsers don't recognize it. Here's one solution.

Inspired by this [article](http://sassbreak.com/viewport-relative-headings-with-sass/) from Sass Break, which outlines the `$vw_context/$vw_base` method.

## The code
```sass
@mixin vw_with_fallback($font_sizes, $vw_base) {
	$vw_context: (1280*.01) * 1px;
	$vw_font_size: ($vw_base/$vw_context) * 1vw;

	@each $list in $font_sizes {
		$i: index($font-sizes, $list);
		$curr_bp: nth($list, 1);
		$curr_fallback: nth($list, 2);

		@if $i == 1 {
			@media screen and (min-width: #{$curr_bp}) {
				font-size: $curr_fallback;
			}
		}

		@else {
			$use_vw: nth($list, 3);

			@if $use_vw {
				@media screen and (max-width: #{$curr_bp}) {
					font-size: $curr_fallback;
					font-size: $vw_font_size;
				}
			}
			@else {
				@media screen and (max-width: #{$curr_bp}) {
					font-size: $curr_fallback;
				}
			}
		}
	}
}
```

## Usage
```sass
$hed_font_sizes:
	1281px 160px,
	1280px 80px true,
	767px 60px true,
	400px 50px false;

h1 {
	@include vw_with_fallback($hed_font_sizes, 100px);
}
```

### In $hed_font_sizes:
+ The first value per list is the breakpoint at which the font size takes effect. The first breakpoint the mixin receives is used as a `min-width` media query because it assumes you'll want the font size to cap out at a certain point. That's also why it has no third value (more on that later). All the other breakpoints are `max-width`.
+ The second value is the font size to use instead of `vw` at a given breakpoint. This is good for browser support and also if you want to instead use an `em`, `px`, `pt` or `%` value at a certain breakpoint.
+ The third value is true if you want to use the `vw` value at that breakpoint, false if you'd rather use the second value.

### In `@include`
+ The first argument is the list, the contents of which is explained above.
+ The second argument is used to calculate `vw`. Sending 100px to the mixin means that, at a window width of 1280px (the $vw_context), the `h1` should have a font size of 100px. You can make the $vw_context whatever value you want. (I encourage you to read the Sass Break [article](http://sassbreak.com/viewport-relative-headings-with-sass/) that outlines this method.)

## Test it
+ `npm install`
+ `grunt sass`
+ Open **/example/index.html**

## License
MIT
