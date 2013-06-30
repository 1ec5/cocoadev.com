

General/ABMultiValue Inherits from General/NSObject
----

Represents values of type kABMultiXXXXProperty. All values in an General/ABMultiValue must be of the same type.  e.g. in a kABMultiStringProperty: all values must be strings

If you need to store away a reference to a specific value/label pair use the "identifier".

You cannot use the Index to reference because other apps can add/remove/reorder a multivalue making your index point to the wrong pair. Identifiers are unique Ids.


----

<code>- (unsigned int)count;</code>

 *Returns the number of value/label pairs

    
<code>- (id)valueAtIndex:(int)index;</code>

 *Returns a value at a given index
 *Raises an exception if index is out of bounds


<code>- (General/NSString *)labelAtIndex:(int)index;</code>

 *Returns a label at a given index
 *Raises if index is out of bounds

    
<code>- (General/NSString *)identifierAtIndex:(int)index;</code>

 *Returns a identifier at a given index
 *Raises if index is out of bounds

    
<code>- (int)indexForIdentifier:(General/NSString *)identifier;</code>

 *Returns the index of a given identifier
 *Returns General/NSNotFound if not found

    
 
<code>- (General/NSString *)primaryIdentifier;</code>

 *Identifier for the primary value

    

<code>- (General/ABPropertyType)propertyType;</code>

 *Type of this multivalue (kABMultiXXXXProperty)
 *Returns kABErrorInProperty in this multi-value is empty or not all values have the same type.