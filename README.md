<HTML>
<HEAD>
<TITLE>update-nessus-plugins</TITLE>
<LINK REV="made" HREF="mailto:theall@tifaware.com">
<LINK REL="shortcut icon" HREF="http://www.tifaware.com/favicon.ico">
</HEAD>

<!--#include virtual="/header.html"-->
<!--#if expr="$weblint" -->
<BODY BGCOLOR="#FFFFFF">
<!--#endif -->


<H4 ALIGN="center">
   <A HREF="/">TifaWARE</A> |
   <A HREF="../">Perl Programs</A> |
   update-nessus-plugins
</H4>
                                                                                
                                                                                
<HR>
<H2>update-nessus-plugins</H2>

<H3>Introduction</H3>

<P><A HREF="http://www.nessus.org/">Nessus</A> is a high-quality and
free remote vulnerability scanner.  While its initial cost is certainly
attractive, arguably its main advantage is its extensive and continually
evolving plugin database of vulnerability checks.  Nessus comes with a
shell script to retrieve the latest set of plugins from a central
repository -- <B>nessus-update-plugins</B> -- and I would imagine most
Nessus users run this fairly often to keep their plugins up-to-date.</P>

<P>While the script does at fine job, it does have a couple of
limitations in my opinion: it does not backup existing plugins, it
doesn't attempt to validate new or changed plugins, and it doesn't
provide much information about changes.  I designed
<B>update-nessus-plugins</B> to address these limitations and to call
<B>nessus-update-plugins</B> to do the actual updates so as to avoid any
problem of compatibility.</P>

<P><B>update-nessus-plugins</B> is written in Perl and calls
<B>nessus-update-plugins</B> to update the plugins as well as <A
HREF="../describe-nessus-plugin/">describe-nessus-plugin</A> to obtain
descriptive information about new / changed plugins.  It should work on
any unix-like system with Perl 5 or better (Perl 5.005 if you choose to
generate summary reports).  It also requires the following Perl
modules:</P>

<UL>
   <LI><CODE>Algorithm::Diff</CODE></LI>
   <LI><CODE>Archive::Tar</CODE></LI>
   <LI><CODE>Carp</CODE></LI>
   <LI><CODE>Digest::SHA</CODE></LI>
   <LI><CODE>File::Find</CODE></LI>
   <LI><CODE>Getopt::Long</CODE></LI>
   <LI><CODE>IO::Zlib</CODE> (used by <CODE>Archive::Tar</CODE> to output file 
      in compressed form)</LI>
   <LI><CODE>POSIX</CODE></LI>
</UL>

<P>If your system does not have these modules installed already, visit
<A HREF="http://search.cpan.org/">CPAN</A> for help.  Note that
<CODE>Algorithm::Diff</CODE>, <CODE>Archive::Tar</CODE>, and
<CODE>IO::Zlib</CODE> are not included with Perl distributions and that
<CODE>Digest::SHA</CODE> is not included with Perl distributions prior
to 5.8.0 so you may need to install them yourself.</P>


<H3>Installation</H3>

<OL>
   <LI>Retrieve the script 
      <A HREF="/code/update-nessus-plugins/update-nessus-plugins">update-nessus-plugins</A> and save it
      locally.  You may also wish to verify its 
      <A HREF="/code/update-nessus-plugins/update-nessus-plugins.sha256sum">SHA256 checksum</A> or, better, its
      <A HREF="/code/update-nessus-plugins/update-nessus-plugins.asc">GPG signature</A> against
      <A HREF="/~theall/gpg.html">my current GPG key</A>.</LI>
   <LI>Verify ownership and permissions on the script - it will need to
      run as root to run <B>nessus-update-plugins</B>.</LI>
   <LI>You may wish to edit the script to adjust the location of
      the Perl interpreter in the first line and to initialize variables
      such as <CODE>$lang</CODE> and <CODE>$plugins_dir</CODE> to 
      suit your environment and tastes.</LI>
   <LI>Ensure you have working versions of <B>nessus-update-plugins</B>
      and, if you wish to do summary reports, 
      <A HREF="../describe-nessus-plugin/">describe-nessus-plugin</A>.</LI>
</OL>


<H3>Use</H3>

<P>There are several commandline arguments you can use to override 
variables defined in the script itself:</P>

<BLOCKQUOTE>
<DL>
   <DT>-b, --backup</DT>
   <DD>Create a backup of existing plugins first.</DD>

   <DT>-d, --debug</DT>
   <DD>Display debugging messages while running.  <B>Note: this will
      still update the plugins!</B></DD>

   <DT>-i, --ignores</DT>
   <DD>Ignore the specified files found in the plugins directory from
      the summary report and parse check, overriding <CODE>@ignores</CODE>.</DD>

   <DT>-p, --parse</DT>
   <DD>Parse new / changed plugins and report whether errors exist.</DD>

   <DT>-s, --summary</DT>
   <DD>Print a summary report of new / changed plugins.</DD>
</DL>
</BLOCKQUOTE>

<P>Notes:</P>
<UL>
   <LI>Commandline arguments take precedence over variables defined in the
      script.</LI>

   <LI>Enabling the debug option will not prevent plugins from being 
      updated.</LI>

   <LI>Specifying a file either in <CODE>@ignores</CODE> or with the 
      <CODE>-i</CODE> option won't prevent it from being updated by
      <B>nessus-update-plugins</B>, just from being included in any
      summary report and parse check. It will, however, be included in
      the backup, if one is made.</LI>

   <LI>Multiple files to ignore can be specified on the commandline either
      by a comma-delimited string or by multiple argument pairs. For example,
      <CODE>-i "MD5,README"</CODE> is equivalent to <CODE>-c MD5 -c 
      README</CODE>.</LI>
</UL>

<P>Examples:</P>
<BLOCKQUOTE>
<DL>
   <DT><CODE>update-nessus-plugins</CODE></DT>
   <DD>updates nessus plugins. Note: this does nothing more than call
      <B>nessus-update-plugins</B>.</DD>

   <DT><CODE>update-nessus-plugins -b</CODE></DT>
   <DD>creates a backup of old plugins and updates plugins.</DD>

   <DT><CODE>update-nessus-plugins -bs</CODE></DT>
   <DD>creates a backup, updates plugins, and prints a summary of 
      new / changed plugins.</DD>

   <DT><CODE>update-nessus-plugins -p</CODE></DT>
   <DD>updates plugins and parses new / changed plugins for errors.</DD>
</DL>
</BLOCKQUOTE>


<H3>Known Bugs and Caveats</H3>

<P>Currently, I am not aware of any bugs in this script.</P>

<P>You need a working version of <B>nessus-update-plugins</B> to use
this script.  If you are having trouble getting that to work, please
join <A HREF="http://list.nessus.org/">nessus@list.nessus.org</A> and
ask for assistance there.</P>

<P>If you wish to generate summary reports of new / changed plugins, you
also need <A
HREF="../describe-nessus-plugin/">describe-nessus-plugin</A> version
2.01 or better.</P>

<P>If you encounter an error similar to <CODE>Insecure dependency in
chdir while running with -T switch at /usr/lib/perl/5.00503/File/Find.pm
line 133</CODE> when trying to run <B>update-nessus-plugins</B>, it's
likely that you're using an older version of the Perl module
<CODE>File::Find</CODE>.  Versions distributed with Perl versions prior
to 5.6.0 don't support the <CODE>no_chdir</CODE> option, which is used
in this script to avoid problems with taint checks.  The solution is to
either upgrade <CODE>File::Find</CODE>, upgrade Perl itself, or disable
taint checks (ie, remove the <CODE>-T</CODE> option on the first line of
the script).</P>

<P>The option for parsing new / changed plugins requires that the NASL
interpreter support the <CODE>-p</CODE> option, which was introduced
with Nessus version 2.0.7.</P>

<P>If you encounter a problem with this script, I encourage you to rerun
it in debug mode (eg, add <CODE>-d</CODE> to your commandline) and
examine the resulting output before contacting me.  Often, this will
enable you to resolve the problem by yourself.</P>


<H3>Copyright and License</H3>

<P>Copyright (c) 2003-2010, George A. Theall.<BR>
All rights reserved.</P>

<P>This script is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.</P>


<H3>History</H3>

<CENTER>
<TABLE CELLPADDING="5" CELLSPACING="0" WIDTH="95%" BORDER="1">
   <TR>
      <TH ALIGN="left">Date </TH>
      <TH ALIGN="left">Version </TH>
      <TH ALIGN="left">Verification </TH>
      <TH ALIGN="left">Notes<BR></TH>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">31-Jan-2010 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.37">1.37</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.37.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.37.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
        <LI>Changed location of plugins directory.</LI>
        <LI>Ignored anything other than files when taking snapshots of the plugins directory.</LI>
        <LI>Switched from MD5 to SHA256 checksums.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">14-Dec-2008 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.36">1.36</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.36.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.36.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
        <LI>Removed support for setting language since it's obsolete in Nessus.</LI>
        <LI>Ignore .bootstrap.done in plugins directory.</LI>
        <LI>Explicitly ignore nlib's.</LI>
        <LI>Don't croak if a plugin doesn't exist.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">27-May-2008 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.35">1.35</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.35.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.35.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Avoided parsing binary files.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">17-May-2008 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.34">1.34</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.34.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.34.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Avoided diffing binary files.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">12-Aug-2007 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.33">1.33</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.33.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.33.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Avoided warnings related to nbins.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">03-Jul-2006 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.32">1.32</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.32.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.32.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Fixed compile error in regex added in 1.31.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">02-Jul-2006 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.31">1.31</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.31.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.31.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Added <CODE>/opt/nessus/bin</CODE> to the default PATH.</LI>
         <LI>Changes are only reported for specific types of plugins.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">19-Jul-2004 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.30">1.30</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.30.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.30.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Changed the bang-path to use <CODE>/usr/bin/perl</CODE> rather
            than <CODE>/usr/local/bin/perl</CODE>.</LI>
         <LI>Changed the usage message.</LI>
         <LI>Added code to display the usage message if option parsing fails.</LI>
         <LI>Added code to ignore selected files in the plugins directory from
            the summary report and the parse check along with the associated
            option <CODE>-i</CODE> to specify files from the commandline.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">09-Dec-2003 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.20">1.20</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.20.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.20.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Added <CODE>-h</CODE> option as a synonym for <CODE>--help</CODE>.</LI>
         <LI>Added option for selecting preferred language.</LI>
         <LI>Replaced <CODE>print STDERR</CODE> with <CODE>warn</CODE>
            throughout the script.</LI>
         <LI>Renamed variable <CODE>$lang_pref</CODE> to 
            <CODE>$lang</CODE>.</LI>
         <LI>Replaced code used in summary report to parse descriptive 
            information from a plugin with a call to 
            <B>describe-nessus-plugin</B>.</LI>
         <LI>Removed descriptive info from summary report for any plugins
            that are include files since the info will always be empty.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">12-Oct-2003 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.11">1.11</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.11.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.11.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Made minor changes in the documentation.</LI>
         <LI>Removed the superfluous variable <CODE>@report</CODE>.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">10-Oct-2003 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.10">1.10</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.10.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.10.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Added support for BugTraq IDs and X-References in the summary 
            report.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">03-Sep-2003 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.02">1.02</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.02.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.02.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Fixed another glitch in reporting plugin information that would
            arise if comments were not anchored at the start of lines.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">30-Jun-2003 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.01">1.01</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.01.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.01.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Fixed a glitch that could lead to erroneous information about
            plugins being reported because comments in scripts were not
            ignored.</LI>
      </UL></TD>
   </TR>
   <TR>
      <TH ALIGN="left" VALIGN="top">04-Jun-2003 </TH>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.00">1.00</A> </TD>
      <TD ALIGN="left" VALIGN="top"><A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.00.sha256sum">SHA256 checksum</A> / 
         <A HREF="/code/update-nessus-plugins/update-nessus-plugins-1.00.asc">GPG</A> </TD>
      <TD ALIGN="left" VALIGN="top">
      <UL>
         <LI>Initial version.</LI>
      </UL></TD>
   </TR>
</TABLE>
</CENTER>


<HR>
<H4 ALIGN="center">
   <A HREF="/">TifaWARE</A> |
   <A HREF="../">Perl Programs</A> |
   update-nessus-plugins
</H4>

<!-- $Id$ -->
                                                                                
<!--#include virtual="/footer.html" -->
<!--#if expr="$weblint" -->
</BODY>
<!--#endif -->
</HTML>