cadgol -- a cad-native modeling language
========================================

Modeling real-world objects with computers traditionnally requires using
graphical user interfaces to design, model and simulate such objects and their
interactions and reactions to external objects and forces. Such an approach is,
at first, intuitive since the designer is visually interacting and "crafting"
the object as they go. As explained by Jessie Frazelle
[here](https://github.com/EmbeddedVentures/community), there are limits to this
approach and there's a need for a programming-like language for the creation of
complexe shapes using text-based entry similar to a software development
process.

Unfortunately, such a language doesn't presently exist. Or, at the very least,
the existing examples don't provide all the necessary constructs and
capabilities of their fully-featured graphical-based counterparts. Hence,
there's a need to design something that aims at providing the same level of
functionality as a modern CAD tool but based on programming-style paradigms.
But objects aren't software. While some of their features could be usefully
repeated using math-like software expressions, objects are infinitely more
complex in their variety and functionality. There is no "Von Neuman" model for
objects. Looking at traditional software languages alone is therefore not a very
good starting point.

If software alone won't cut then are there already languages and semantics
spaces that can be used to model things other than software? The immediate
obvious answer are RTL/HDL-languages such as Verilog and VHDL. They generally
comprise two types of construct: sequential and combinatorial circuits. They
somewhat look similar but synthesize into different constructs at the hardware
level ... which real physical structures as the final output in the case of ASIC
fabrication. That, though, isn't necessarily why HDLs are most interesting. The
more interesting thing about RTL is how we eventually got to how it's done
today.

Already in the '60s it appears there were attempts at describing hardware using
[software-like languages](https://en.wikipedia.org/wiki/Hardware_description_language#History).
The seminal work, though, seems to be Bell and Newell's 1971 "Computer
Structures: Readings and Examples". In this book, Bell and Newell catalog dozens
of processors and, most importantly, devise a language and notation to describe
those processors in a uniform fashion. They also introduce the term
"register-transfer level" which they define as "The components of an RT system
are registers and functional transfers between registers", which is precisely
what Verilog, VHDL and other modern-day HDLs do. The Instruction Set Processor
(ISP) notation which they describe in their book is then further refined in
further articles and used in the seminal "Introduction to VLSI Systems" by Meade
and Conway.

Now, why is that Bell and Newell so important to the present analysis? Well,
because what lands Bell and Newell to devise ISP is their need to catalog a very
wide variety of systems using a single descriptive language. Which then begs the
question. Is there are catalog of real-world objects that provides us with, if
nothing else, the proper semantic space to properly describe complex real-world
objects in a fashion that fully encompasses their functionality in the same was
a modern day CAD tool would?

It turns out that not only such a thing exists, but it's been being worked on
for a few hundred years and is likely one of the most important pilars of the
world economy. What could that possibly be you ask? Well, simply put,
... patents and, more specifically, the language of patent claims. The fact of
the matter is that in order to be granted, patent claims **must** describe an
invention, typically a real-world physically-structured object, in a
sufficiently unique fashion as to distinguish it from all previously-devised
objects, be they in patents or elsewhere. Consequently, an unimaginable amount
of human intellectual effort has been invested over time in elaborating and
evolving the precision and accuracy of patent claim language to describe all
features and structures of objects humans can envision. There is, therefore,
fertile ground in looking at patent claim language as the basis of a CAD-grade
descriptive language.

Which brings us the current proposal. Based on the needs and analysis above,
what is proposed is a C-like descriptive language that uses patent-claim-like
statements to describe real-world objects for the purpose of being "compiled",
simulated and otherwise processed by tools for enabling designers to create
and analyze the behavior of complex objects. This is, by its very nature, a
rough cut meant to elicit feedback and further discussion. There is likely much
work to be done in specifying semantics, conventions and validating the
usefulness and feasibility of such an approach. Nevertheless, here is what's
proposed as being "cadgol", a cad-native modeling language.

cadgol example:
===============

Let's start with a cadgol "Hello World!". Here's a mahogany chair in cadgol:

```
/*
 * mohagony-chair.cgol
 *
 * A mohagony chair with 4 legs
 *
 * (C) Karim Yaghmour, 2021
 *
 * [ ASL ]
 */

/* The components we need and their local names */
comprises chair.leg as leg_fl, leg_fr, leg_bl, leg_br;
comprises chair.seat as main_seat;
comprises chair.back as main_back;

/* Material */
madeof wood.mohagony.honduran;

/* Fastners */
connector fastners.glue.wood.generic as glue;

/* Initialize component sizes */
leg_fl { dia: 1; height: 16 };
leg_fr { dia: 1; height: 16 };
leg_bl { dia: 1; height: 16 };
leg_br { dia: 1; height: 16 };
main_seat { width: 20; depth: 18, height: 1, leg_dia: 1 };
main_back { width: 10; depth: 1, height: 20 };

/* Assemble components */
assemblage {
    main_seat->leg->front->left  += glue(leg_fl);
    main_seat->leg->front->right += glue(leg_fr);
    main_seat->leg->back->left += glue(leg_bl);
    main_seat->leg->back->right += glue(leg_br);
    main_seat->back += glue(main_back);
}
```

The `comprises` statements here indicate the "library" components we are
including and the local instances of said components. There's presumably a
library called `chair` which has such things as `leg`, `seat` and `back`. Those
are assumed to be generic enough to be used in different chairs.

The `madeof` statement indicates the material this object is made of and is
assumed to come from a global library of materials. Materials likely provide the
object with color, mass, thermal conductivity, electrical connectivity, Young's
modulus, bending stiffness, etc. An individual object is likely made of a single
material (otherwise use the `composite.` library ;) ), although a complex object
could be made of several parts made of several differently-typed components. In
this case, the chair is made of Honduran Mohogany, and it's assumed that the
components included didn't themselves already have materials specified for
them. Hence, we can separate describing a part in mathematical terms from the
type of material that's used to make it.

The `connector` is whatever can be used to connect parts together. In this case
we're using glue as a fastener, and it's a generic wood glue. In a more complex
object we can imagine that other types of connectors could be used such as
welding, injection molding, etc.

Now that we've listed what parts are needed, what the present object is made of,
and the type of connectors used to put it together, we provide some specific
parameters about the components. In this case the description is provided as a
CSS-like set of values based on the types of parameters that can be used to
dictate the part's specifics.

Finally we assemble the object together by connecting the parts together based
on the anchors available for each part. The `+=` semantics are used to indicate
that it's possible that many objects may connect at a given anchoring
point/surface/axis. Think for example of an injection-molded part which has
many subparts being joined at a specific location.

That's the overall chair.

If we just take a look at the seat, it would look something like this:

```
/*
 * chair/seat.cgol
 *
 * A generic chair seat for a chair with 4 legs and a back
 * ...
 */

/**
 * Seat model
 * @width:           seat width
 * @depth:           seat depth
 * @height:          seat height
 * @leg_dia:         leg diameter (assuming round pegs)
 *
 * Models a seat as a basic cube with round pegged legs
 *
 * Note: some variables are implicitly available in all "modeledas()"
 * functions. "anchor" is such a variable.
 */
modeledas(int width, int depth, int height, int leg_dia) {
	object o;
	surface s;
	contact c, r;
	int recess_x, recess_y;

	/* A very simple seat */
	o = cuboid(width, depth, height);

	/* Connectors for legs */
	s = o.bottom();
	c = circle(leg_diameter);
	recess_x = 0.1 * s.x_size;
	recess_y = 0.1 * s.y_size;
	anchor.add("leg->front->left") = planar_intersect(s, c, recess_x, recess_y);
	anchor.add("leg->front->right") = planar_intersect(s, c, s.x_size - recess_x, recess_y);
	anchor.add("leg->back->left") = planar_intersect(s, c, recess_x, s.y_size - recess_y);
	anchor.add("leg->back->right") = planar_intersect(s, c, s.x_size - recess_x, s.y_size - recess_y);

	/* Connectors for back */
	s = o.top();
	r = rectangle(x, 0.1 * y);
	anchor.add("back") = planar_intersect(s, r, 0, s.y_size);
}	
```

The `modeledas` statement takes parameters coming from the initialization of the
part and actually creates the part based on those parameters while also
specifying the anchors to be defined for connecting to this part.  In this case,
the seat is a basic cuboid and recessed anchoring points for attaching legs at
its bottom, and an anchoring point for the back. More complex formulas could be
used used to specify the object or we could leave it open for inclusion of
hand-drawn objects in some IDE such as the ones provided by classic CAD
tools. This would be similar to the way Verilog/VHDL enable including
parts/components/libraries which are "hand drawn" to a given manufacturing
process and **arent't** themselves expressed using RTL.

The legs and back could also be specified similarly in chair/leg.cgol and
chiar/back.cgol using modelededas() statements such as the above. Entire
libraries could then be created for providing different parts of parts ready to
be assembled into larger objects. Again, those individual parts many not
themselves have materials or dimensions specified for them, just mathematical
formulas or a set of spacially-located interconnected points in the case of
externally drawn items.

Tooling
=======

As a starting point, a language such as the above should be compilable into the
file formats used by existing CAD software such as SolidWorks, FreeCAD,
OpenSCAD. Hence, if nothing else, we should be able to use all existing toolsets
to work with the output of a compiled cadgol file. Such a cadgol compiler could
take as a parameter the type of output format we are interested in, much like
`gcc` can take the machine type as a parameter when compiling C code.

Further down the road, though, the more interesting thing would be have tools
and IDEs that can work with this format natively. There's no reason, for
instance, that an IDE couldn't render me the above legs and chair and enable me
to double-click on any of the parts to edit the cadgol file that generated that
part.  From there we can then envision integrating into there all the features
of a modern-day CAD software.

One of the benefits of working with text-based descriptions, of course, is the
ability to actually commit the results into software-style code tracking systems
like git. A difference in materials for the above chair, for example, would show
up as:
```
-madeof wood.mohagony.honduran;
+madeof wood.oak.red;
```

Misc.
=====

This proposal obviously only scratches the surface. There's much to be done to
fully specify what a language such as cadgol should/could do. There's nothing
here specifying constraints for movement. That too, however, can heavily borrow
from patent-like terminology to describe movement.

Open invitation
===============

I'd like to thank Jessie Frazelle for having taken the time to properly phrase
the problem that needed to be solved in the post mentioned above. I've had this
itch for a long time, but, first, I didn't think anyone else was interested,
and, second, I never really thought of taking to time to lay out the problem
description in a useful fashion for others to look at. I had the opportunity to
bounce this rough sketch of an idea off Jessie during a call a few days ago and
it seemed to hold water, which is another reason why I'm encouraged to share
this since CAD software internals aren't my field in any way and this is just my
limited view of the problem. Hopefully others, more informed, will find this
useful in some way, shape or form. But, fwiw, I'd use this right now if it
existed.

