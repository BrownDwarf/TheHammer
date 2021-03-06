TheHammer
---

HAMMER.PRO V1.2 README

## OVERVIEW

Hammer.pro is an idl code written to replace the need for several 
different independent spectral pipelines used primarily by the 
UW M star research group.  Each spectra hit by the Hammer is analyzed to 
predict its MK spectral type on the basis of a fit to a set of 30 spectral 
indices, taking into account the uncertainty in each index.  The spectra
is then analyzed to provide information on the activity level of the star,
as measured by the presence or absence of chromospheric Halpha emission, 
as well as for M subdwarf status, by allowing calculation of the ratio of 
TiO to CaH.  Once the entire set of spectra submitted to the Hammer has
been automatically analyzed in this manner, the user is allowed to 
interactively tweak the assigned spectral type before a final summary file
is created.  

## FORMATS

The Hammer reads in spectra in a variety of formats, namely as sdss fits 
files ('sdssfits'), a standard fits spectra ('fits'), or text files 
with a wavelength column preceeding a column of fluxes ('txt').  Version 1.1 
has added a new format ('princeton') to read in the Princeton 
reductions of SDSS spectra.  This new format depends on the READSPEC program
distributed as part of the IDLSPEC2D package -- users will need to install
the IDLSPEC2D, IDLUTILS, and Goddard IDL utilities available from
http://www.astro.princeton.edu/~schlegel/code.html

All formats should have wavelengths expressed in angstroms; sdss spectra should
be in the standard sdss wavelength format (vacuum wavelengths & 
logarithmic pixels), while non-sdss spectra should be calibrated in air
wavelengths.

## USE

Before being run the first time, the user add the hammerv1_1 directory to 
their idl path, and then must edit line 19 of hammer.pro to indicate the 
location of the hammerv1_1 directory in their system.  These steps are 
required so that hammer.pro can locate and load the templates.idl and 
indices.idl files contained within that directory.

MAC USERS: to make the cursor input for eyecheck.pro work correctly, 
type the following on the command line: 

> defaults write com.apple.x11 wm_click_through -bool true

typing this once will allow you to use the clickable IDL interface
in the eyetyping portion of the Hammer.

Hammer.pro should then be passed a table giving the data paths of spectra to be 
analyzed in the first column, with a second column giving the format of each 
type of spectra using the coding system indicated in the 'FORMATS' section.
The hammer will then allow the user to specify a S/N level below which
spectra should be ignored rather than included in the following analysis.  
S/N is measured from the 100 Angstrom wide region with the largest mean flux; 
the three possible regions have central wavelengths of 4550, 6150 and 8250.
Spectra which pass this S/N cut are then measured automatically for a best 
fit MK spectral type, Halpha emission equivalent width (and the statistical 
significance of the activity level thereby detected), and various spectral
indices which can indicate subdwarf status.  

Once all spectra have been measured automatically, the user is prompted
to determine if another S/N cut should be used to pare the list which
will be examined visually.  EYECUT then asks what spectra to start with --
this should usually be zero, unless the user has stopped the program 
previously and is now returning to type the remaining spectra.  If the user is 
returning to eyetype the remaining spectra, there is an option to bypass
the automatic spectral typing, subdwarf and magnetic activity portions of
the code.

The template for the automatically determined best fit spectral type is 
then compared to the target spectra.  The user can select other templates
from the same MK sub class (Os, As, Gs, Ms, etc.) by clicking on the 
appropriate label along the top axis.  Labels shown in green indicate valid
MK spectral types; the current template selection is shown in red.  Along the
left axis of the plot are labels which, if clicked, allow the user to compare
the target to templates from other MK sub classes.  The appropriate template
labels then replace those along the top axis of the plot.  Along the right
hand side of the screen are the 'EARLIER' and 'LATER' buttons, which allow
the user to step through the template set one sub-class at a time. 

Buttons along the bottom allow the user to modify the comparison routine
in some additional manner:

ODD: after selecting odd, the user is prompted via the IDL XTERM window to 
enter a 3 letter (or less) code for the object's spectral classification.
Possible notations could include C* for Carbon star, QSO for Quasars, 
Sdm for subdwarfs, WDM for White dwarf M dwarf pair, etc.  The routine
records this in place of the automatically fit spectral type, and loads
the next target spectra.

BAD: indicates the spectra has such low S/N it should be discarded from
the sample.  Object has a spectral type recorded as 'bad' and the next
target spectra is loaded for visual analysis.

SMOOTH: for noisy spectra, allows smoothing to facilitate comparison of 
the continuum shape with smoothed template spectra.  Possible pixel 
smoothing window widths are displayed for the user's consideration along the
top axis of the plot window.  Following this selection, subsequent template
comparisons will be conducted for this spectra using the same smoothing 
window.  Users can choose to revert to the original spectrum by choosing a 
smoothing window of 1 in a subsequent SMOOTH operation.  

DONE: the current template is assigned as the visually confirmed type.

BACK: returns to the spectra considered immediately previous to this one.
As this is a kludgey feature, it reads in and re-writes the visual 
summary file.  For long lists, this could take a significant amount of time.

BREAK: allows the user to save the results of spectral typing partway through 
the list.  Returning to Hammer.pro will allow the user to continue visually 
typing spectra from the same location in the list, though automatic 
determination of spectral types won't be circumvented. 

## OUTPUTS

autoSpTresults.tbl -- results from the automatic determination of spectral 
type via 30 spectral indices.  Columns are: the filename, the spectral format,
the S/N of the spectra, and the automatically assigned spectral type.

ew.tbl -- results from A. West's subdwarf and activity codes.  See the code
themselves (in the /neededpros directory) to determine what each column is.

eyeout.tbl -- a minimal output from the eyecheck portion of the code, 
recording the filename, visual type, and automatically assigned type.

finalsummary.tbl -- a summary file containing all the outputs in one large
text file.  See column headings for more info.  

rejectedspectra.tbl -- a file indicating spectra whose S/N were too low to 
be considered by Hammer.pro.  

## TEST FILES

Included in this distribution are a set of test spectra and a sample list
to serve as examples for users.  'testsoe.lis' is a list of input spectra
for the Hammer, pointing to spectra contained within the testspecs directory.

To run the Hammer with these test files, one should start up IDL.  the Hammer
can then be started from within the hammer directory with the following 
sequence of commands:

IDL> .r hammer.pro
IDL> hammer, 'testsoe.lis'

Good luck!

## AUTHORS

Hammer.pro uses a spectral typing engine created by Kevin R. Covey using
a set of indices determined to track well with spectral type from
measurements of a set of over 1500 calibration spectra (thanks to the 
authors of the STELIB, INDO-US, and MILES spectral atlases -- the 
value of these indices as a function of spectral type is given in the IDL
save file located at /neededpros/indices.idl).  K. Covey also incorporated
the SUBDWARF and EW routines written by A. West for analysis of the activity
and metallicity of M dwarfs, with slight modification to allow the processing
of non-sdss spectra (I may have messed this up -- if you use the outputs
of these routines and are getting odd answers, that's probably what happened).
K. Covey also heavily modified the original EYECHECK routine by A. West to
allow the comparison of target spectra to the entire range of MK spectral 
types, as well as adding new functionality (the ODD, SMOOTH, and BACK buttons, 
etc.)  The creation of all the routines above were performed with the steady
supervision of Suzanne Hawley and the UW astronomy Dept.

## CHANGES

1/4/07  -- Changed cursor command option to /down instead of 3 to allow
	  eyecheck.pro to work correctly on macs.

	-- modified README file to include command necessary for macs
	  to allow cursor command to work correctly.

	-- Fixed incorrect format statement for printing S/N of spectra
	  rejected from eyecheck.pro.

	-- Added 'princeton' format for princeton reduced SDSS spectra.

1/30/07 -- Fixed bug that crashed eyecheck.pro when 'earlier' and 'later' 
	  buttons are used at the edge of the spectral sequence

        -- Fixed bug that prevented huge numbers of spectra from going through
	   ew.pro

7/6/07  -- Modified readplainfits.pro to handle spectra where the reference
	  pixel is not the first pixel in the spectrum

7/9/07  -- Fixed bug where spectra without any measurable indices were not 
	  being written to rejectedspectra.tbl

        -- Made ew.pro, eyecheck.pro, and hammer.pro all output 100 
	  character filenames in their output tables.

12/4/09 -- Modified default window size in eyecheck.pro

        -- removed superfluous print statements and unnecessary comments 

12/7/09 -- removed double 'vactoair' call after verifying that indices were 
           measured from spectra that were not double 'vactoair'ed.

        -- added ability to specify partial paths in list of spectra

11/7/14 -- Forked by gully