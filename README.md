# perl_hardware_gen
#This script is used to generate the module def according to the excel describing the hardware interface.

#!/usr/bin/perl
$in=$ARGV[0];
if(!defined $in) {
    die "Usage:$0 filename";
}

my $out=$in;
$out=~s/(\.\w+)?$/.v/;

if(!open $in_fh, '<',$in) {
    die "cannot open '$in':$!";
}

if(!open $out_fh, '>',$out) {
    die "cannot open '$out':$!";
}

while(<$in_fh>)  {
    chomp;
    my @line = split(',',$_);
    if($line[0] eq "module") {
        print $out_fh "module $line[1] (\n";
    }
    elsif ($line[0] eq "parameter") {
        my @pam = split(';',$line[1]);
        my $pam_len = @pam;
        if($pam_len > '1') {
            $pam_first = splice(@pam,1);
            $pam_last = splice(@pam,-1);
            print "$pam_first\n";
            print "$pam_last\n";
            print $out_fh "#(parameter $pam_first,\n";
            foreach $ele (@pam) {
                print $out_fh "            $ele,\n";
            }
            print $out_fh "            $pam_last )\n";
        }
        elsif($pam_len == '1') {
            print $out_fh "#(parameter $pam[0])\n";
        }
                
    }
    elsif($line[0] eq "Name") {
        print $out_fh "(\n";
    }
    elsif(lc($line[0]) eq "end") {
            print $out_fh ");\n";
    }
    if($line[3] ne "last") {
        if($line[2] == '1') {
            if(uc($line[1]) eq 'I') {
                print $out_fh "input    $line[0],\n";
            }
            elsif (uc($line[1]) eq 'O') {
                print $out_fh "output   $line[0],\n";
            }
            elsif (uc($line[1]) eq 'IO') {
                print $out_fh "inout    $line[0],\n";
            }
        }  
        else {
            if(uc($line[1]) eq 'I') {
                print $out_fh "input    [$line[2]-1:0] $line[0],\n";
            }
            elsif (uc($line[1]) eq 'O') {
                print $out_fh "output   [$line[2]-1:0] $line[0],\n";
            }
            elsif (uc($line[1]) eq 'IO') {
                print $out_fh "inout    [$line[2]-1:0] $line[0],\n";
            }
        }
    }
    elsif(lc($line[3]) eq "last") {
        if($line[2] == '1') {
            if(uc($line[1]) eq 'I') {
                print $out_fh "input    $line[0]\n";
            }
            elsif (uc($line[1]) eq 'O') {
                print $out_fh "output   $line[0]\n";
            }
            elsif (uc($line[1]) eq 'IO') {
                print $out_fh "inout    $line[0]\n";
            }
        }  
        elsif(lc($line[3])) {
            if(uc($line[1]) eq 'I') {
                print $out_fh "input    [$line[2]-1:0] $line[0]\n";
            }
            elsif (uc($line[1]) eq 'O') {
                print $out_fh "output   [$line[2]-1:0] $line[0]\n";
            }
            elsif (uc($line[1]) eq 'IO') {
                print $out_fh "inout    [$line[2]-1:0] $line[0]\n";
            }
        }
    }
}
print $out_fh "\n";
print $out_fh "\n";
print $out_fh "endmodule\n";

######################################################################
module	lte_bp_secure		
parameter	AXI_AW=32;AXI_DW=32		
Name	I/O	Width	
clk_sys	I	1	
clk_core	I	1	
rstn_in	i	1	
rstn_in_sys	I	1	
awid_s	I	1	
awaddr_s	I	AXI_AW
awlen_s	I	1	
awsize_s	I	1	
awburst_s	Io	4	
awlock_s	I	1	
awcache_s	IO	5	
awprot_s	I	1	
awvalid_s	I	1	
awready_s	o	1	
wid_s	I	1	
wdata_s	i	1	last
end			
