

I'm trying to find out if there's a way to get the contents of a view (only the contents, not the whole view) as an image. Specifically I'd like to get the rendered content of a webview as an image (and since a General/WebView is also an General/NSView, I assume this can be applied to views in general).

I've tried a few things, like getting a bitmap or PDF representation of the view, but the image always ends up looking exactly as the view is displayed onscreen, and not just its contents.

----

you need to get the representation from the interior view, not the scrolling view. for a General/WebView, that'd be      General/[webView mainFrame] frameView] documentView]

I've already tried that, and it doesn't make any difference - it's as if it gets the whole view, not just the content. Say if the content is 300px wide, but the view is 800px wide, when I get an image, even using that method, I still get something that looks like how it's displayed onscreen, and not just the 300px wide content.

* if you want the content to be a different size, make a new view of that size, render the content into there, and grab your image.*

[snide comments truncated]
----

I don't think there's a way to determine the size of the contents, so it's probably not possible to resize it to what you need unless you already know what size you need, which would make the whole question pointless.

I'm guessing you want something much like how it would be printed out or saved to PDF; have you tried to run an [[NSPrintOperation on the view, saving it to file, and using that as the image?

* I did try something like that, but didn't go into details with the printInfo in my experiments so was getting much of the same results. I'll play around with adding options and trying again, as this is probably the only way I'll get the closest representation of what I want.*

----

After looking a bit at the General/WebKit private headers, I've determined that one **can** get pretty much just the (scaled) content of a webview (and webview only), and only as a printOperation. The following code acomplishes this; the trick is to save the operation to file, and create an image from that file.
    
-(General/NSImage *)createImage:(General/WebFrame *)frame
{
	// create thumbnail of the page
	
	General/NSImage *image;
	id docview=General/frame frameView] documentView];
	
	// make sure prefs set to print bg
	BOOL bgSetting=[[[frame webView] preferences] shouldPrintBackgrounds];
	[[[frame webView] preferences] setShouldPrintBackgrounds:YES];

	[[NSPrintInfo *pi=General/[NSPrintInfo sharedPrintInfo];
	[pi setJobDisposition:General/NSPrintSaveJob];
	General/pi dictionary] setObject:@"/path/to/save.pdf" forKey:[[NSPrintSavePath];
	General/pi dictionary] setObject:[[[NSNumber numberWithInt:1] forKey:General/NSPrintFirstPage]; // only want a preview
	General/pi dictionary] setObject:[[[NSNumber numberWithInt:1] forKey:General/NSPrintLastPage];  // so only first page
	General/pi dictionary] setObject:@"Letter" forKey:[[NSPrintPaperName];  // desired image size
	[pi setBottomMargin:0.0];
	[pi setTopMargin:0.0];
	[pi setLeftMargin:0.0];
	[pi setRightMargin:0.0];
	
	General/NSPrintOperation *op=General/[NSPrintOperation printOperationWithView:docview printInfo:pi];
	
	[op setShowPanels:NO]; // don't show panels
	
	[op runOperation];
	
	// restore webview preferences setting
	General/[frame webView] preferences] setShouldPrintBackgrounds:bgSetting];
	
	// get the image as data, and resize appropriately	
	image = [[[[[NSImage alloc] initWithContentsOfFile:@"/path/to/save.pdf"] autorelease];
	[image setScalesWhenResized:YES];
	[image setBackgroundColor:General/[NSColor clearColor]];
	[image setSize:General/NSMakeSize(200,260)]; // max size we want, with a letter size ratio
	return image;
}


Takes a few seconds to respond the first time, but subsequent calls are a bit faster. Don't know if there's a way to speed that up.

-Seb

----

The problem with doing the General/NSPrintOperation is that it uses the print stylesheet.  If you want the view as rendered on-screen, use the following  (this should work for any view):

    
- (General/NSImage *)createImageFromView:(General/NSView *)view {
	General/NSRect rect = [view bounds];
	General/NSBitmapImageRep *imageRep = [view bitmapImageRepForCachingDisplayInRect:rect];
	[view cacheDisplayInRect:rect toBitmapImageRep:imageRep];
	General/NSImage *image = General/[[[NSImage alloc] initWithSize:rect.size] autorelease];
	[image addRepresentation:imageRep];
	
	return image;
}


To use this with a webView, pass it General/[webView mainFrame] frameView] documentView]

Note that this will be problematic if the web view uses plugins. At present I don't know of a way to capture a complete image of a [[WebView that contains plugin content.

----

I had something almost identical to the above code trying to create an General/NSImage of view, but kept getting weird artifacts in the final image. It turns out you need to clear the initial bitmap that you are handed!

    
- (General/NSImage *)createImageFromView:(General/NSView *)view {
	General/NSBitmapImageRep *imageRep = [view bitmapImageRepForCachingDisplayInRect:[view bounds]];
	unsigned char *bitmapData = [imageRep bitmapData];
	if (bitmapData != NULL) bzero(bitmapData, [imageRep bytesPerRow] * [imageRep pixelsHigh]);
	[view cacheDisplayInRect:[view bounds] toBitmapImageRep:imageRep];
	General/NSImage *image = General/[[NSImage alloc] init];
	[image addRepresentation: imageRep];
	
	return image;
}


- Jeffrey J. Early

----