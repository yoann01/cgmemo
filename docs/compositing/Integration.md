# The Beauty Grade

The Beauty Grade is the first process to be applied to CGI in order to start modelling its Characteristic Curve that
will recreate the way the sensor/film captured the footage. This is a chronological Step-by-Step guide to assist you
through this vast process. In order to follow this guide correctly you should be familiar with photography basics of
exposition, elements of optics relative to the behaviour of light intensities vs densities, film/sensor supports, NUKE™
Grade & Toe mathematical operations and digital color fundamentals.

# Beauty Grade Guide

- [x] Viewer Gamma high (recommended at 4).
- [x] Grade node (attached to the resulting CG).
- [x] (un)premultiply by rgba.alpha.
- [x] Black Levels:
>- Blackpoint: sample the darkest pixel from the CG.
   Remember: The intensity of light is relative to exposure but absence of light –black– is absolute.
>- Making a selection (rectangle) of the area containing the darkest pixels – please exclude any pixel from
    outside the premultiplied area.
>- Using the Pixel Analyser use the Min colour chip and drag & drop into the colour chip of the Blackpoint knob.
>- Lift: sample the darkest area from the Scan.
>- Making an accurate selection (rectangle) of the area containing the darkest pixels. Make the selection as big
    as possible of just pure “black” pixels.
>- Using the Pixel Analyser use the Average (or the Median when you have an uneven selection of pixel values)
    colour chip and drag & drop into the colour chip of the Lift knob.
- [x] Base & Fog:
>- Toe node (attached to the Grade node).
>- (un)premultiply by rgba.alpha.
>- Link the Lift knob from the Grade node to the Toe (lift knob).
    *To create a link, drag & drop the curve icon by holding the Ctrl/Command key (if successful a green arrow will appear between the nodes).
>- Back to the Grade node again: we push down the black levels to crash against the Toe levels of the base & fog
by decreasing the value of the Offset knob.
    *This is something you do by eye, make sure you lose the same amount of detail in the black levels as your scan.
>- Check any transfer unbalance (RGB) from the black levels to the lower midtones (that colour halo).
    >- TMI color controls of the Offset knob (start with the T [temperature] control (slider) and then move into the
    M [magenta] control (slider), don’t touch the I [intensity] control (slider), because you already adjusted in
    previous operations).
