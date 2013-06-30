How do you create an General/NSOutlineView with multiple roots?

- General/JohnDevor

Huh? ... Doesn't that, like, defy the laws of nature or something?

----

Why not just pretend that root siblings are root nodes?

----

Create multiple outline views, or one outline view with a popup at top to switch between roots, or use a General/NSBrowserView

----

When your data source methods     -outlineView:numberOfChildrenOfItem: and     -outlineView:child:ofItem: are called with     nil as the item, that's the outline view asking you for the root objects.  Simply return the number of root objects to the first method, and the respective root objects themselves to the second method, and you'll be in business.  -- Bo

----

Ah, thanks Bo. - General/JohnDevor

----

I'm having trouble trying to keep the root objects straight. I tried an array, but I don't really understand how the calling part would work. Could anyone help me with this?

I've got some sketchy code like this:

    
static int i = 0; // this is probably a horrible way to cycle through the items...
- (id)outlineView:(General/NSOutlineView *)ov child:(int)index ofItem:(id)item
{
    // is the parent non-nil?
    if (item)
        // Return the child
        return [item childAtIndex:index];
    else 
        // Else return the root
        i++;
        return [rootNodeArray objectAtIndex:i]; // this should cycle through my array... I think... probably not (and I see it only works once)
        
}


- General/JohnDevor

----

You don't need to cycle through your root array.  It will do that for you, i.e. for each root item you have, it will call     -outlineView:child:ofItem: with the appropriate index.  So ditch the static variable and just return     [rootNodeArray objectAtIndex:index].  -- Bo

----

Ah! The frustration... Thanks again Bo.