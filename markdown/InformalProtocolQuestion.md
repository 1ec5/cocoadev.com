Moved from General/InformalProtocols.

----

If an informal protocol is used with a category on General/NSObject, does this mean that all objects will automatically have these methods declared? Do you do something like this:

**General/CustomInformalProtocol.h"**
    
@protocol General/CustomInformalProtocol <General/NSObject>
- (void)customMethodForInformalProtocol;
@end

@interface General/NSObject (General/CustomInformalProtocol) <General/CustomInformalProtocol>
@end


And then for your subclass that will implement the protocol: 

**General/CustomObject.h"**
    
@interface General/CustomObject : General/NSObject <General/CustomInformalProtocol> {

}
@end


**General/CustomObject.m"**
    
@implementation General/CustomObject
- (void)customMethodForInformalProtocol {
    /*
             custom method implementation
    */
}
@end


Also, if I implement the protocol methods in a category of General/CustomObject can I move the protocol declaration from the main interface to the category interface?


**General/CustomObject.h"**
    
@interface General/CustomObject : General/NSObject {

}
@end


**General/CustomObject.m"**
    
@implementation General/CustomObject
@end


**CustomObject_CustomInformalProtocolCategory.h"**
    
#import "General/CustomObject.h"

@interface General/CustomObject (General/CustomInformalProtocol) <General/CustomInformalProtocol>
@end


**CustomObject_CustomInformalProtocolCategory.m"**
    
@implementation General/CustomObject (General/CustomInformalProtocol) 
- (void)customMethodForInformalProtocol {
    /*
             custom method implementation
    */
}
@end


---- 

I just put an example informal protocol on the General/InformalProtocols page, see if that helps.  I moved this off of that page because your use of bold and code blocks is very eye catching, but in this case the code would be misleading.  

*If an informal protocol is used with a category on General/NSObject, does this mean that all objects will automatically have these methods declared?*

Yes.  Well, all objects that inherit from General/NSObject.

*Do you do something like this:<snip>*

Not so much.  Take a look at the example.  Also, if you aren't doing something like declaring delegate or datasource methods, I'd suggest going for a General/FormalProtocol.

-General/KenFerry

----

OK, I understand now. I was thinking that an informal protocol still needs a defined     @protocol. So informal protocols do not support conformance testing. Then I think my real question is related to General/FormalProtocols. In my example above. The thing that I am wondering is if you make a category for General/NSObject that declares a protocol as being part of it's interface, does this mean that all objects will also have the protocol's interface declared? 

----

Some of what you're asking is in funky corner cases that should not come up in valid uses of protocols.  So I'm not sure that the answers are guaranteed not to change between system versions.  Instead let me tell you what you definitely can do, then address your questions.

First:  An object conforms to a protocol if and only if either its class or one of its super classes declares its conformance.  You declare conformance like this

    
@interface General/MyObject : General/NSObject <General/ProtocolWeConformTo>
 

or like this

    
@interface General/MyObject (General/ProtocolCategory) <General/ProtocolWeConformTo>
 

Conforming to a formal protocol is a matter of claiming it, it isn't the same as declaring or implementing all the methods that a protocol specifies. 

Methods in formal protocol are not optional.  If you have 

    
@interface General/MyObject (General/ProtocolCategory) <General/ProtocolWeConformTo>
 

in your source, you're expected to have

    
@implementation General/MyObject (General/ProtocolCategory) 
 

matching it, and it should implement all the methods from the protocol.

Okay, now for your questions.

*The thing that I am wondering is if you make a category for General/NSObject that declares a protocol as being part of it's interface, does this mean that all objects will also have the protocol's interface declared? *

Yes, objects of class General/NSObject and all subclasses will conform to the protocol. There's no utility in putting a category on General/NSObject that claims conformance to a protocol - you might as well put a normal category on General/NSObject and forget about the protocol.  In a slight weirdness, if you do this 

    
@interface General/MyObject (General/ProtocolCategory) <General/ProtocolWeConformTo>
 

but don't have a matching implementation, the compiler will not complain, the methods will be declared on General/NSObject and all subclasses, but objects of class General/NSObject will not return     YES to     -General/[NSObject conformsToProtocol:].  You shouldn't count on that, protocol methods are not optional.

*Also, if I implement the protocol methods in a category of General/CustomObject? can I move the protocol declaration from the main interface to the category interface? *

Yes, as discussed above.


Anyway, you should tell us what you want to do.  You may not be asking the right questions here.

-General/KenFerry

----

Many thanks, I just needed a double check on how things work with preprocessing. The thing I thought you "HAD" to do with formal protocols was I thought that the interface for the class proper (not a category interface) had to declare the protocol for conformance to work (I guess I could have just tried it but I didn't like "not knowing"). I have been declaring formal protocols in the interfaces for classes and never in a interface for a category. This forced me to put empty method implementations of the protocol methods in the class implementation to avoid warnings. I hated doing this. 

I guess it's overkill to do this:

    
@interface General/MyObject (General/ProtocolCategory) <General/ProtocolWeConformTo>
 

Pointing out that this does not guarantee that     conformsToProtocol: will return     YES makes me wonder if this method uses runtime introspection to decide if an object conforms to a protocol. I haven't made any applications that load bundles to add categories to an object. I think I read somewhere that you can do this. I wonder if conformance can be achived dynamically. Maybe I'll play with a simple app to see if this is the case.

Anyways thanks for clearing many things up.

----

No problem.  

*Pointing out that this does not guarantee that     conformsToProtocol: will return     YES makes me wonder if this method uses runtime introspection to decide if an object conforms to a protocol.*

Welll, this was the thing that surprised me.  It almost seems like a bug.  As long as there is a matching implementation that *will* get you a     YES to     conformsToProtocol:.   That's even if the implementation doesn't implement all the protocol methods, which will garner a warning.  And there really should be a matching implementation, otherwise it's a misuse of protocols (since you're supposed to implement all the methods of protocols you conform to).

It does use introspection, though that doesn't mean checking what methods area implemented.  Every class object maintains a list of protocols that it conforms to (at least that's my memory).

Oh, and I don't think it's overkill to put the protocol methods in a separate category.  Did you mean that something else was overkilll? 

Anyway, glad you got your problems straightened out.  

-General/KenFerry

----

My font was small when I read (and copy/pasted) the sample code. I thought it said     General/NSObject not     General/MyObject. I don't think using a category for     General/MyObject is overkill. I thought creating a category of     General/NSObject just for protocol declaration was overkill.