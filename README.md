# libYgl
libYgl emulates SGI's GL routines under X11
<!--- $Id: ReadMe.html,v 3.6 2005-02-03 11:43:25+01 fred Exp fred $ --->

<H1> <IMG SRC="Smile.gif" ALIGN=MIDDLE ALT="Logo">
Ygl Version 4.3 Description </H1>

<H4>
<FONT SIZE=4>&copy;</FONT>opyright 1993-2025 by
<A HREF="/~fred/">Fred Hucht (fred(AT)thp.uni-due.de)</A>
</H4>
<P>

<CITE><B>Ygl</B></CITE> is distributed in terms of the GNU Lesser
General Public License (LGPL). Please see the file
<A HREF="LICENSE">LICENSE</A> for further details. You may not
distribute this project without this file (ReadMe.html) and the
license file included. 
<P>
Here you can read the <A HREF="Changes.html">Changes history</A> and
the <A HREF="FAQ.html">Ygl FAQ</A>.

<H2> Description </H2>

<CITE><B>Ygl</B></CITE> emulates <A HREF="#SGI">SGI's GL</A> routines
under X11. It compiles fine under AIX >3.2, HP-UX >7.0, Linux
with XFree/Xorg, SunOS, ConvexOS, Mac A/UX, Mac OS/X, macOS, and many others,
but needs an ANSI-C compiler (gcc is ok). It was written for two reasons:

<UL>
 <LI> On our RS/6000 GT4 hardware, 2D <CITE><B>Ygl</B></CITE> is up to
      twenty times faster (<TT>circf()</TT>) than GL (strange, isn't it?...) 
 <LI> 2D (and 3D using OpenGL) graphics runs on non GL hardware and
      even on remote X-Servers. 
</UL>

Included are most of the two-dimensional graphics routines, the queue
device routines, the query routines, doublebuffering, RGB mode with
dithering, window attribute routines, FORTRAN bindings and more (see
below). 
<P>
3D stuff was added with version 4.0 using <A HREF="http://www.OpenGL.org">OpenGL</A> calls.
<P>

Since Version 2.8, all <CITE><B>Ygl</B></CITE> functions do have a
<A HREF="/cgi-bin/Man.pl?fortran">FORTRAN</A> interface. The
FORTRAN versions of all functions have an underscore appended, so the
FORTRAN compiler must append a '_' to all function names.
<A HREF="/cgi-bin/man.pl?f2c">f2c</A> does this,
<A HREF="/cgi-bin/Man.pl?xlf">xlf</A>
(under AIX 3.2) requires the option <TT>-qextname</TT>. See
<A HREF="smile_f77.f">smile_f77.f</A> for an example program. Set
<TT>FortranBindings</TT> to <TT>0</TT> in Imakefile or change
Makefile.std if you don't want these bindings. FORTRAN bindings are
not tested because I have no programs.f to test. If you find bugs,
feel free to report them to me. 
<P>

You can always find the latest version of <CITE><B>Ygl</B></CITE> in
the directory 
<A HREF="ftp://ftp.thp.uni-due.de/pub/source/X11/">ftp.thp.uni-due.de/pub/source/X11/</A>.
<P>
Note that <A HREF="http://www.perl.org">perl</A>,
<A HREF="http://www.python.org">python</A>
and <A HREF="http://www.tcl.tk/">tcl</A> bindings
for Ygl are available
<A HREF="http://www.aero.iitm.ernet.in/~prabhu/software/python.html">here</A>
thanks to Prabhu Ramachandran.

<H2> Details </H2>

<H3> Environment variables: </H3>

Several environment variables are referenced in the library to control
the behavior of <CITE><B>Ygl</B></CITE>:
<UL>
  <LI>  The environment variable <B>YGL_USEOGL</B> or
	<B>YGL_OGL</B> can be set to 1 to tell
	<CITE><B>Ygl</B></CITE> to use OpenGL bindings. The library
	must have been compiled with OpenGL bindings, see Imakefile.
	See also examples/lmbind.c.

  <LI>  The environment variable <B>YGL_PRIVATECOLORMAP</B> or
	<B>YGL_PCM</B> can be set to 1 to force
	<CITE><B>Ygl</B></CITE> to use a private colormap. While GL
	always uses a private colormap, <CITE><B>Ygl</B></CITE> uses
	the global map by default to avoid technicolor effects on
	X-Servers not capable of more than one colormap at a
	time. <B>Note</B> that you can only redefine the color of
	already drawn objects when you use a private colormap. 

  <LI>	The environment variable <B>YGL_FLUSHTIME</B> or <B>YGL_FT</B>
	(default value: 0.1) can be used to adjust the time in seconds
	between flushes of the graphics pipeline. It can be set to 0
	to disable the automatic update via interval timer. Setting
	YGL_FLUSHTIME to -1 will put the X-Server into synchronous
	mode (see <A HREF="/cgi-bin/man.pl?XSynchronize">XSynchronize</A>).
	This mode seems to be veeeryyy slooow, but is good for
	debugging... 
	

  <LI>  The environment variable <B>YGL_BACKINGSTORE</B> or
	<B>YGL_BS</B> can be set to 1 to enable BackingStore support
	for all <CITE><B>Ygl</B></CITE> windows. With BackingStore,
	the X-Server remembers the contents of a window when it gets
	obscured or iconified. You must start your X-Server with the
	option "-bs" ("+bs" on some systems) to use this feature. 

  <LI>  The environment variables <B>YGL_CMAPVISUAL</B> or
	<B>YGL_CV</B> and <B>YGL_RGBVISUAL</B> or <B>YGL_RV</B>
	control the selection of visuals for colormap mode and RGB mode. Set
	these variables to the visual id of the desired visual as
	returned by xdpyinfo (both hex and decimal is ok). Setting one
	of these variables to -1 forces <CITE><B>Ygl</B></CITE> to use
	the default visual of the screen instead of the best visual.

  <LI>  The environment variable <B>YGL_SINGLEGC</B> or <B>YGL_GC</B>
	can be set to 1 to force <CITE><B>Ygl</B></CITE> to use a
	single graphics context for colormap windows. Per default
	<CITE><B>Ygl</B></CITE> uses one GC for each color, i.e. 256
	GCs for a 8 bit colormap window, because this speeds up
	drawing with many color changes by a factor of 2 or so on many
	systems. Using a single GC may be faster if you use many
	mapcolor calls as in colormap animation. <B>Note</B> that if
	the used colormap visual has more than 256 colors YGL_GC
	defaults to 1. 

  <LI>  The environment variable <B>YGL_PARENTWID</B> or
	<B>YGL_PWID</B> controls the parent window of
	<CITE><B>Ygl</B></CITE>. Per default the root window of the
	default screen of the given display is the parent window of
	windows opened with winopen(). Set this variable to the window
	id of the desired window as returned by xwininfo (both hex and
	decimal is ok).

  <LI>  <A NAME="dither"></A>The environment variable
	<B>YGL_DITHERSIZE</B> or <B>YGL_DSZ</B> controls dithering in
	RGB mode. With this variable you can specify the size of the
	dither matrix. The default value is 4, leading to a 4x4 dither
	matrix. Valid values range from 0 to 6. On a normal 8 bit
	TrueColor visual ({3,3,2} bits {red,green,blue}) you obtain
	113x113x49 = 625681 different "colors" with YGL_DSZ=4, leading
	to an effective screen depth of approx. 19 bits. The other
	values and the effective screendepth are:
<PRE>
         |  8 bit (3-3-2)  | 12 bit (4-4-4)  |  16 bit (6-5-5)
  YGL_DSZ| # colors (bits) | # colors (bits) |  # colors (bits)
  -------------------------------------------------------------
    0,1  |      256 ( 8  ) |     4096 (12  ) |     65536 (16  )
     2   |    10933 (13.4) |   226981 (17.8) |   3953125 (21.9)
     3   |   114688 (16.8) |  2515456 (21.3) |  44531200 (25.4)
     4   |   625681 (19.3) | 13997521 (23.7) | 249232081 (27.9)
     5   |  2354176 (21.2) | 53157376 (25.7) | 949029376 (29.8)
     6   |  6976981 (22.7) |158340421 (27.2) |2831006341 (31.4)
</PRE>
	<B>Note</B> that dithering costs speed. You may set YGL_DSZ=0
	to disable dithering. If you are lucky and have a 24 bit 
	TrueColor visual, YGL_DSZ is automatically set to zero.
</UL>

<H3> Doublebuffering: </H3>

<CITE><B>Ygl</B></CITE> supports doublebuffering via the XServer
extensions "DOUBLE-BUFFER" (new in X11R6.1) and "Multi-Buffering". The
related routines are <TT>doublebuffer()</TT>, <TT>swapbuffer()</TT>
and <TT>singlebuffer()</TT>.
<P>
<B>Notes:</B>
<DL>
<DT>OpenGL:<DD>
   You can use the routine <TT>gsync()</TT> to wait for the next
   vertical retrace. If you use doublebuffering, it might help to set
   the environment variable <TT>__GL_SYNC_TO_VBLANK</TT> to 1 to swap
   buffers at a vertical retrace.
<DT>X11R6.1:<DD>
   If you have X11R6.1 or later, set <TT>DoubleBuffer</TT> to 1 in
   Imakefile.
<DT>AIX &lt; 3.2.5:<DD>
   The extension "Multi-Buffering" is new in X11R5 and is included in
   the lpp X11dev.src under AIX. To use doublebuffering you have to
   link the "Multi-Buffering" extension into your X-Server (under AIX,
   see /usr/lpp/X11/Xamples/README, "Linking the Sample Extensions
   into the Server").
<DT>AIX &gt;= 3.2.5:<DD>
   You don't need to recompile the X-Server anymore to use
   doublebuffering under AIX 3.2.5 (AIXWindows 1.2.3). You simply have
   to add the option "-x mbx" to the X-Servers command line on
   startup.
<DT>AIX &gt;= 4.1:<DD>
   You need the LPP X11.adt.ext to compile <CITE><B>Ygl</B></CITE>
   with doublebuffering enabled. 
</DL>

<H3> RGB mode: </H3>

<CITE><B>Ygl</B></CITE> can run in a per window RGB mode, if the
X-Server has a TrueColor or DirectColor
visual. <CITE><B>Ygl</B></CITE> will always select the appropiate
visual for the desired mode, independent of the default visual, i.e.:

<PRE>
 Colormap mode <-> PseudoColor, StaticColor,
                   GrayScale, StaticGray,
                   TrueColor or DirectColor visual

 RGB mode      <-> TrueColor or DirectColor visual
</PRE>

A RGB mode window is a child of the initially created colormap mode
window. <CITE><B>Ygl</B></CITE> uses the property
<TT>WM_COLORMAP_WINDOWS</TT> to tell the window manager, which
colormaps are to be installed. To toggle the colormaps when a RGB
window is subwindow of a colormap window, use the window manager
function <TT>f.colormap</TT> in <A HREF="/cgi-bin/man.pl?twm.n">twm</A>
or <TT>f.next_cmap</TT> in <A HREF="/cgi-bin/Man.pl?mwm">mwm</A>.

<CITE><B>Ygl</B></CITE> will automatically dither all output if the
X-Server doesn't support 24 bits. See the section
<A HREF="#dither">environment variables</A> for details.

<H3>Multiple Expose events (REDRAW) in qread:</H3>

<CITE><B>Ygl</B></CITE> ignores all Expose events that have
e.xexpose.count != 0 (see
<A HREF="/cgi-bin/man.pl?XExposeEvent">XExposeEvent</A>(3X11)) AND all
Expose events when another Expose event for the same window is in the 
Event queue. This procedure minimizes unnessesary
redraws. Nevertheless, a REDRAW event is generated when a window is
moved even when the contents of the window does not have to be
redrawn. This is to be compatible with SGI's GL, where no extra event
for window moves exist :-(

<H3>Automatic flushing of the graphics pipeline:</H3>

The process virtual interval timer (see
<A HREF="/cgi-bin/Man.pl?setitimer">setitimer</A>(3))
is used to flush the graphics pipeline every 0.1 seconds. This
interval is adjustable (see above). The system functions sleep() and
usleep() are covered by the library to flush the pipeline, too. This
had to be done because the GL hardware flushes the pipeline
automatically on every vertical retrace and has no command to
flush. <P>

<B>Note</B> that sleep() and usleep() always return 0. <P>

You can use "sleep(0)" to flush the graphics pipeline explicitly.
If you have problems with sleep() and usleep(), undefine COVERSLEEP in
header.h. 

<H2> Supported routines </H2>
Finally a list of supported GL routines is included. For a detailed
description of the routines see the GL manuals in the Info-Explorer
(under AIX) or click at the command below.

<H3>Window related routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/minsize.html">minsize</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/maxsize.html">maxsize</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/prefsize.html">prefsize</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/prefposition.html">prefposition</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/stepunit.html">stepunit</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/keepaspect.html">keepaspect</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winopen.html">winopen</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winclose.html">winclose</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/gconfig.html">gconfig</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/gexit.html">gexit</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/wintitle.html">wintitle</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winset.html">winset</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winget.html">winget</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getsize.html">getsize</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getorigin.html">getorigin</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getxdpy.html">getXdpy</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getxdpy.html">getXwid</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getbutton.html">getbutton</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getvaluator.html">getvaluator</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winmove.html">winmove</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winposition.html">winposition</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/ginit.html">ginit</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winpop.html">winpop</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winpush.html">winpush</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/windepth.html">windepth</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/swinopen.html">swinopen</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winconstraints.html">winconstraints</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/noport.html">noport</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/noborder.html">noborder</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winx.html">winX</A>.

<BR><B>Note:</B>
The routine <TT>winX(Display *dpy, Window win)</TT> will
ignore the first argument <TT>dpy</TT>. <CITE><B>Ygl</B></CITE>
always uses the XServer specified in the environment variable DISPLAY. 

<H3>Color related routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rgbmode.html">RGBmode</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rgbcolor.html">RGBcolor</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/cmode.html">cmode</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/color.html">color</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/mapcolor.html">mapcolor</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getplanes.html">getplanes</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getmcolor.html">getmcolor</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getmcolors.html">getmcolors</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getcolor.html">getcolor</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/grgbcolor.html">gRGBcolor</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/c.html">c3s, c3i, c3f</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/cpack.html">cpack</A>.

<H3>Device related routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/tie.html">tie</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/noise.html">noise</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/isqueued.html">isqueued</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/qdevice.html">qdevice</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/unqdevice.html">unqdevice</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/qreset.html">qreset</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/qtest.html">qtest</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/qread.html">qread</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/qenter.html">qenter</A>,
 <BR>

<B>Notes:</B>
<UL>
<LI>  Currently supported devices are: 
  REDRAW, KEYBD, INPUTCHANGE, LEFTMOUSE, MIDDLEMOUSE, RIGHTMOUSE
  (MENUBUTTON), MOUSEX, MOUSEY, WINQUIT, and all individual keys as
  F1KEY, ESCKEY, UPARROWKEY, and so on.

<LI>  qenter does only support the REDRAW device.
</UL>

<H3>Menu routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/defpup.html">defpup</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/newpup.html">newpup</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/addtopup.html">addtopup</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/dopup.html">dopup</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/freepup.html">freepup</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/setpup.html">setpup</A>.

<BR><B>Note:</B>
The routines <TT>addpup()</TT> and <TT>defpup()</TT>
accept an additional modifier '%M'. If you define a submenu anchor
with '%M' instead of '%m', <CITE><B>Ygl</B></CITE> calls the function
defined with '%F' with the two arguments <TT>menu-return</TT> and
<TT>submenu-return</TT>, i.e.: 

<PRE>
int callback(Int32 m, Int32 s) {
  sleep(s);
  switch(m) {
  case 1: explode(); break;
  case 2: implode(); break;
  }
  return(m &lt;&lt; 8 + s);
}

main() {
  ...
  sub  = defpup("Seconds%t|1|2|3|4|5");
  menu = defpup("Menu%t%F|Explode%M|Implode%M",	callback, sub, sub);
  ...
}
</PRE>
See <TT>examples/popup.c</TT> for details.

<H3>Font routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/loadxfont.html">loadXfont</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/font.html">font</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getfont.html">getfont</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getfontencoding.html">getfontencoding</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getheight.html">getheight</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getdescender.html">getdescender</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/strwidth.html">strwidth</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/charstr.html">charstr</A>.

<H3>Doublebuffer routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/singlebuffer.html">singlebuffer</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/doublebuffer.html">doublebuffer</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/swapbuffers.html">swapbuffers</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/frontbuffer.html">frontbuffer</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/backbuffer.html">backbuffer</A>.

<H3>Misc routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/winpop.html">gflush</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/gsync.html">gsync</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/gversion.html">gversion</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rectzoom.html">rectzoom</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rectread.html">lrectread</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rectwrite.html">lrectwrite</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rectcopy.html">rectcopy</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/readsource.html">readsource</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rectread.html">rectread</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rectwrite.html">rectwrite</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/readpixels.html">readpixels</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/writepixels.html">writepixels</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/readrgb.html">readRGB</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/writergb.html">writeRGB</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/blendfunction.html">blendfunction</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/linewidth.html">linewidth</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getlwidth.html">getlwidth</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/deflinestyle.html">deflinestyle</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/setlinestyle.html">setlinestyle</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getlstyle.html">getlstyle</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lsrepeat.html">lsrepeat</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getlsrepeat.html">getlsrepeat</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/logicop.html">logicop</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getdisplaymode.html">getdisplaymode</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getgdesc.html">getgdesc</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/setbell.html">setbell</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/ringbell.html">ringbell</A>.

<H3>Coordinate transformation routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/ortho.html">ortho2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/viewport.html">viewport</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getviewport.html">getviewport</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/reshapeviewport.html">reshapeviewport</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pushviewport.html">pushviewport</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/popviewport.html">popviewport</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getmatrix.html">getmatrix</A>.

<H3>Drawing routines:</H3>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/clear.html">  clear</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pnt.html">    pnt2s,  pnt2i,  pnt2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/move.html">   move2s, move2i, move2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rmv.html">    rmv2s,  rmv2i,  rmv2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/draw.html">   draw2s, draw2i, draw2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rdr.html">    rdr2s,  rdr2i,  rdr2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/arc.html">    arcs,   arci,   arc</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/arcf.html">   arcfs,  arcfi,  arcf</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/circ.html">   circs,  circi,  circ</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/circf.html">  circfs, circfi, circf</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rect.html">   rects,  recti,  rect</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rectf.html">  rectfs, rectfi, rectf</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/sbox.html">   sboxs,  sboxi,  sbox</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/sboxf.html">  sboxfs, sboxfi, sboxf</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/concave.html">concave</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pmv.html">    pmv2s,  pmv2i,  pmv2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rpmv.html">   rpmv2s, rpmv2i, rpmv2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pdr.html">    pdr2s,  pdr2i,  pdr2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rpdr.html">   rpdr2s, rpdr2i, rpdr2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pclos.html">  pclos</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/poly.html">   poly2s, poly2i, poly2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/polf.html">   polf2s, polf2i, polf2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/bgnpoint.html">     bgnpoint,      endpoint</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/bgnline.html">      bgnline,       endline</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/bgnclosedline.html">bgnclosedline, endclosedline</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/bgnpolygon.html">   bgnpolygon,    endpolygon</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/v.html">    v2s, v2i, v2f, v2d</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/cmov.html">   cmov2s, cmov2i, cmov2</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getcpos.html">getcpos</A>.

<BR><B>Note:</B>
It's not possible to change drawing attributes (color, ...)
in the middle of a vertex pipeline in <CITE><B>Ygl</B></CITE>.
This affects the routines
<TT>{bgn|end}{point|line|closedline|polygon}()</TT> and
<TT>pdr(), rpdr()</TT>. 

<H3>3D routines</H3>
<H4>Drawing:</H4>

 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/c.html">      c4s,    c4i,    c4f</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/cmov.html">   cmovs,  cmovi,  cmov</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pnt.html">    pnts,   pnti,   pnt</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/move.html">   moves,  movei,  move</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/draw.html">   draws,  drawi,  draw</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rmv.html">    rmvs,   rmvi,   rmv</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rdr.html">    rdrs,   rdri,   rdr</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pmv.html">    pmvs,   pmvi,   pmv</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rpmv.html">   rpmvs,  rpmvi,  rpmv</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pdr.html">    pdrs,   pdri,   pdr</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rpdr.html">   rpdrs,  rpdri,  rpdr</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/polf.html">   polfs,  polfi,  polf</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/v.html">      v3s, v3i, v3f, v3d</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/v.html">      v4s, v4i, v4f, v4d</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/swaptmesh.html">swaptmesh</A>.
<H4>Coordinate Systems:</H4>
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/ortho.html">ortho</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lookat.html">lookat</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/window.html">window</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/perspective.html">perspective</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/polarview.html">polarview</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rot.html">rot</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rotate.html">rotate</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/scale.html">scale</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/translate.html">translate</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/loadmatrix.html">loadmatrix</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/multmatrix.html">multmatrix</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pushmatrix.html">pushmatrix</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/popmatrix.html">popmatrix</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getmmode.html">getmmode</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/mmode.html">mmode</A>.
<H4>Polygon specs:</H4>
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/shademodel.html">shademodel</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/n3f.html">n3f</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/normal.html">normal</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/backface.html">backface</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/frontface.html">frontface</A>.
<H4>z-Buffer:</H4>
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/zbuffer.html">zbuffer</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/zclear.html">zclear</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/zdraw.html">zdraw</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/zfunction.html">zfunction</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/czclear.html">czclear</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/depthcue.html">depthcue</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lrgbrange.html">lRGBrange</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lsetdepth.html">lsetdepth</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lshaderange.html">lshaderange</A>.
<H4>Display lists:</H4>
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/genobj.html">genobj</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/isobj.html">isobj</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/makeobj.html">makeobj</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/getopenobj.html">getopenobj</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/closeobj.html">closeobj</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/callobj.html">callobj</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/delobj.html">delobj</A>.
<H4>Misc:</H4>
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lmbind.html">lmbind</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lmcolor.html">lmcolor</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/lmdef.html">lmdef</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/rgbwritemask.html">RGBwritemask</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pushattributes.html">pushattributes</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/popattributes.html">popattributes</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/scrmask.html">scrmask</A>.
<H4>Picking/Names:</H4>
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pick.html">pick</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/endpick.html">endpick</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/picksize.html">picksize</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/initnames.html">initnames</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/loadname.html">loadname</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/pushname.html">pushname</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/popname.html">popname</A>.
<H4>NOPs:</H4>
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/foreground.html">foreground</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/drawmode.html">drawmode</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/iconsize.html">iconsize</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/overlay.html">overlay</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/fullscrn.html">fullscrn</A>,
 <A HREF="https://www.infania.net/misc/rs6000-tl1/manuals/adoclib/libs/gl32tref/endfullscrn.html">endfullscrn</A>.

<H3>Routines not in SGI's GL:</H3>

The following routines are added to standard GL:
<UL>
<LI><TT>GC getXgc(void)</TT><BR>
	return current graphics context. You can for example use it to
	draw lines xor'ed with the background: 
<PRE>
#include &lt;X11/X.h&gt;
...
XSetFunction(getXdpy(), getXgc(), GXxor);
</PRE>
	<B>Note:</B>
	Since version 3.1, getXgc() is not supported anymore, as you
	can use the routine logicop() to set the drawing function. 
<P>

<LI><TT>Window getXdid()</TT><BR>
	return window id of the current drawable. This id differs from
	the window id returned by <TT>getXwid()</TT> when in RGB mode
	or when using doublebuffering.<P>

<LI><TT>Int32 crectread(Scoord x1, Scoord y1, Scoord x2, Scoord y2, Uint8 *data)</TT><BR> 
	read screen data to unsigned char array <TT>data</TT><P>

<LI><TT>void crectwrite(Scoord x1, Scoord y1, Scoord x2, Scoord y2, Uint8 *data)</TT><BR>
	write unsigned char array <TT>data</TT> to screen<P>

<LI><TT>void arcxs(Scoord x,Scoord y,Scoord rx,Scoord ry,Angle s,Angle e)<BR>
void arcxi(Icoord x,Icoord y,Icoord rx,Icoord ry,Angle s,Angle e)<BR>
void arcx(Coord x,Coord y,Coord rx,Coord ry,Angle s,Angle e)</TT><BR>
	draw ellipses<P>

<LI><TT>void arcxfs(Scoord x,Scoord y,Scoord rx,Scoord ry,Angle s,Angle e)<BR>
void arcxfi(Icoord x,Icoord y,Icoord rx,Icoord ry,Angle s,Angle e)<BR>
void arcxf(Coord x,Coord y,Coord rx,Coord ry,Angle s,Angle e)</TT><BR>
	draw filled ellipses<P>

<LI><TT>int gl2ppm(char *filename)</TT><BR>
	write out active <CITE><B>Ygl</B></CITE> window to portable pixmap file
	<TT>'filename'</TT>. When <TT>filename[0] == '|'</TT>, uses
	<TT>popen()</TT> instead of <TT>fopen()</TT>. <B>Note</B> that
	<TT>gl2ppm()</TT> can also be used in a GL application. Just
	add <TT>gl2ppm.o</TT> to the list of object files.
<P> 
</UL>

<H2> Acknowledgements </H2>

The author would like to thank
Michael Staats and Ralf Meyer for some hints and tips.
<P> 
<A NAME="SGI"></A> GL is a registered trademark of
<A HREF="http://www.sgi.com/">Silicon Graphics, Inc.</A>
