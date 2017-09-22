# Reverse-engineer-of-css-flex-dimension-calc
for firefox and chrome

When there is no dim(height/width) property, or it is undefined, null, or auto, it uses the "content" height/width of its children.

When the children have no height to provide the parent(indefinite), the parent will continue to check all descendants.  
If the child is itself content height, the parent will not ask the child to check its own children, and report back, but does it itself. Then the child will traverse the tree again unfortunately, because there's no guarantee the parent will pass down the content calculated. This is dumb and slow.

Not too important but it seems that:  
height, width, and content seem to be done first in a tree traversal, then flex modifications is another seperate traversal starting from the top.

null or auto relinquishes priority, so no way to force "content" height/width if there is stretch, or any flex params (flex params can be forced upon by parent, see _)

stretch = flex-start + 100%(if dim not yet set) [except not = for inheritance, see _]

non-stretch align = align + content

react is default flex

flexDirection is not passed down from parent, column is default if not specified(column can be forced though, see __)

if display prop exists and is non flex(even display:null), it's highjacked by flex parent and forced into display:flex further forcing flex:initial (0 1 auto)* to be default (even with no flex prop); no display prop results in flex:none (0 0 auto)*  
  \* (grow shrink basis)

Forcing display to a non-flex value wont stop default flex options! and cannot be overridden by even definite height/width (in the main axis obviously), so you must do flexShrink:0[and flexGrow:0 flexBasis:null to be safe] (not even flex:none will work because, react doesn't know 'none')  
if display prop exists and is non flex(even display:null), it's highjacked by flex parent to force flexDirection:column!!! not just make it default! unlike the other flex params (to be clear, it'll override an explicit flexDirection:row)

indefinite dim and flexBasis dont contribute(== 0) to parent's content dim for both directions  
Only for width, child's definite flexBasis does not contribute to parent's content width

content width is always passed down.  
chrome : content height is not passed down (except through stretch! (only flexDirection:row obviously))  
firefox: content height is passed down only from column and inline, but not from row or block (except through stretch! (again :row obviously))  
...which is silly since column is like block and row is like inline, ugh.

inherit copies and pastes the prop, doesn't pass down any calculated values from parent.

Main-algo:  
flexBasis(+grow/shrink) -> height/width  
(Remember, flex can be implied and still override explicit height/width, see __)

Cross-algo:  
height/width -> alignSelf -> alignItems(default: stretch)
