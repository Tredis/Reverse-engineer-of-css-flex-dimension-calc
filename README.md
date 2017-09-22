# Reverse-engineer-of-css-flex-dimension-calc
firefox and chrome
When there is no height property, or it is undefined, null, or auto, it uses the "content" height of its children.
When the children have no height to provide the parent, the parent will continue to check all descendants. 
If the child is itself content height, the parent will not ask the child to check its own children, and report back, but does it itself. Then the child will traverse the tree again unfortunately. This is dumb and slow.

null or auto relinquishes priority, so no way to force content height if there is stretch, or any flex params (flex params can be forced upon by parent, see _)
stretch = flex-start + 100%(if dim not yet set) [not the same for inheritance, see _]
non-stretch align = align + content
react is default flex
flexDirection is not passed, column is default if not specified
if not display:flex, display:*** (even display:null ) highjacked by flex parent and forced to display:flex further forcing flex:initial (0 1 auto) to be default (even with no flex prop); no display param results in flex:none (0 0 auto)
Forcing display to a non-flex value wont stop default flex options! and cannot be overridden by even definite height/width (in the main axis obviously), so you must do flexShrink:0[and flexGrow:0 flexBasis:null to be safe] (not even flex:none will work because, react doesn't know 'none')
if not display:flex, display:*** (even display:null ) highjacked by flex parent to force flexDirection:column!!! not just make it default! unlike the other flex params
indefinite dim and flexBasis doesnt contribute to parent's content width
Only for width, child's definite flexBasis does not contribute to parent's content width
when calculating content, all % are zero for both directions
height, width, content done first in a tree traversal, then flex is another seperate traversal starting from the top.

content width is always passed down
chrome : content height is not passed down (except through stretch! (only flexDirection:row obviously))
firefox: content height is passed down only from column and inline, but not from row or block??(except through stretch! (again :row obviously))

Main-algo:
flexBasis(+grow/shrink) -> height/width

Cross-algo:
height/width -> alignSelf -> alignItems(def: stretch)
