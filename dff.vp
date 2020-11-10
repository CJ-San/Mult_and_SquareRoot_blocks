/*******************************************************************************
 * PARAMETERIZATION
 * ****************************************************************************/
//;
//; use POSIX () ;
//; 
//; use strict ;
//; use warnings FATAL=>qw(all);
//; use diagnostics ;
//; 

//; # module parameters
//; my @DEF       = (1,1,1);
//; my $Width     = parameter( Name=>'Width', Val=>[@DEF] , Doc=>"Signal bit widths");
//; ref( $Width ) eq 'ARRAY' or $self->error( "Width param must be array" );
//; my @W ;
//; foreach my $f (@{$Width}){ $W[$#W+1] = $f ;} 
//; my $Dim       = parameter( Name=>'Dim'  , Val=>scalar(@W) , Min=>1 , Step=>1 , Doc=>"Dimensionality of Signal" );
//; scalar( @W ) == $Dim or $self->error( "ERROR dff.vp : Dimension of width array " . scalar( @W ) . " does not match parameter dimension $Dim\n" ); 
//;
//; my $PipelineDepth = parameter( Name=>'PipelineDepth', Val=>1    , Min=>0, Step=>1, Doc=>"Pipeline depth");
//;
//; my $Retime        = parameter( Name=>'Retime'       , Val=>0  , List=>[0, 1] , Doc=>"Pipeline Is Retimeable" ) ;
//; my $Flatten       = parameter( Name=>'Flatten'      , Val=>1  , LIST=>[0, 1] , Doc=>"Flatten and Ungroup" );
//;
//; my $ResetToZero   = parameter( Name=>'ResetToZero'  , Val=>1  , List=>[0, 1] , Doc=>"Flop Resets to Zero" ) ;
//; my $ResetToOne    = parameter( Name=>'ResetToOne'   , Val=>0  , List=>[0, 1] , Doc=>"Flop Resets to One" ) ;
//; ( $ResetToZero and $ResetToOne ) and $self->error("Silly Rabbit: Can't ResetToZero and ResetToOne \n" );
//; my $ResetToRst    = ! ( $ResetToZero or $ResetToOne ) ;
//;
//; my $Enable        = parameter( Name=>'Enable' , Val=>0 , List=>[0,1] , Doc=>"Use Enable Signal" ) ; 
//; my $Valid         = parameter( Name=>'Valid'  , Val=>0 , List=>[0,1] , Doc=>"Use Valid Signal" ) ;
//; my $FlopValid     = parameter( Name=>'FlopValid' , Val=>0 , List=>[0,1] , Doc=>"Flop the valid signal inside this module" );
//;
//; if( $FlopValid == 1 and $Valid == 0 ){ $self->error("ERROR dff.vp : Cannot flop valid, without using it...");  };
//; #Helper Strings for Pixel Slices...
//; my $PS = "[" .  ($W[$#W]-1)  . ":0]" ;
//; my $US = "";
//; for( my $i = 0 ; $i < $#W ; $i++ ){ $US .= "[" . ($W[$i]-1) . ":0]"; } ; 
//;
//;




module `mname` (
       	        input logic  `$PS` din`$US`, 
                input logic  clk, 
	        input logic  rst,

                //; if ( $Enable ) {
		input logic  en, 
                //; }

                //; if ( $Valid ) {
                input logic  d_valid ,
		//;    if ( $FlopValid ){
		output logic q_valid ,
                //;    }
		//; }
	    
                //; if ( $ResetToRst ) {
		input logic  `$PS` rst_val`$US`,
                //; }

                output logic `$PS` q`$US`
              );

//;   
//; ###########################################
//; # SYNOPSYS Synthesis Script  
//; ###########################################
//;
//; #FIXME --> Should refactor these into a utility perl module
  /* synopsys dc_tcl_script_begin
//; if( $Flatten eq 'YES' ){   
   set_ungroup [current_design] true
   set_flatten true -effort high -phase true -design [current_design]
//; }   
//; #   https://solvnet.synopsys.com/dow_retrieve/G-2012.03/manpages/syn2/set_dont_retime.html
//; if( $Retime == 1 ){
   set_dont_retime [current_design] false 
   set_optimize_registers true -design [current_design]
  */
//; } elsif ( $Retime == 0 ){
   set_dont_retime [current_design] true
   set_optimize_registers false -design [current_design]
  */  
//; }
//;

   
//;
//; #If Pipe Depth is 0 then the signal should just be pass through   
//; if ($PipelineDepth==0) {
//;

       assign q = din ;

//;
//; if( $Valid == 1 and $FlopValid == 1 ) {
//;

       assign q_valid = d_valid ;
   
//;   
//; }
//;   

   
//;
//; #If Pipe Depth is greater than 1, instantiate 'N' 1 pipe-stage designs
//; } elsif ( $PipelineDepth > 1  )  { 
//;
//;      for( my $i = 0 ; $i < $PipelineDepth ; $i++ ){
//;
//;              my $pipe   = generate(  'dff', 
//;                                      ( 'pipeIn__' . $i ), 
//;                                        Width=>[@W] ,
//;                                        Dim=>$Dim,
//;                                        PipelineDepth=>1 ,
//;                                        Retime=>$Retime,
//;                                        ResetToZero=>$ResetToZero,
//;                                        ResetToOne=>$ResetToOne,
//;                                        Enable=>$Enable
//;                                     );
//;              my $in = $i==0 ? "din" : ( "imd_" . ($i-1) ) ;
//;              my $in_valid = $i==0 ? "d_valid" : ( "imd_val_" . ( $i-1 ) ) ;

                 logic `$PS`  imd_`$i``$US`;

                 //; if ( $Valid ) {
                    logic        imd_val_`$i`;
		    		 //;}
   
                 `$pipe->instantiate()` ( 
                                           .din(`$in`) , 
					   .q(imd_`$i`) , 
					   //; if ( $Enable ) {
                                           .en(en)  , 
                                           //; }
                                           //; if ( $Valid ) {
                                           .d_valid( `$in_valid` )  ,
					   .q_valid( imd_val_`$i` ) ,
					   //; }
                                           //; if ( $ResetToRst ) {
					   .rst_val(rst_val),
                                           //; }
                                           .clk(clk) ,
                                           .rst(rst) 
                                        );
 
//;   
//;      }
//;

          assign q = imd_`$PipelineDepth-1` ;  
 
          //; if ( $Valid ) {
          assign q_valid = imd_val_`$PipelineDepth-1` ;
          //; }
   
//;
//; # If pipe depth is 1 build the single flop   
//; } elsif ($PipelineDepth == 1 )  {   
//;
   
//;
//;       #If there is no enable, set it to always on
//;       unless ( $Enable ) {
//;

               wire		            en;
               assign en = 1'b1 ;

//;
//;       }
//;
//;
//;      sub set{
//;         my $sig=shift ;
//;         my $val=shift ;
//;         my $dim=shift ; 
//;         my $cDim=shift ;
//;         my $slice=shift ;
//;         my $width=shift ;
//;         my @W = @$width ;
//;         
//;
//;         my $cfgStr = "\n" ; 
//;
//;         if( $cDim == $dim ){
//;            return " assign $sig$slice = $val  ;\n" ;
//;         }
//;         for( my $i = 0 ; $i < $W[$cDim] ; $i++ ){
//;            $cfgStr .= set( $sig , $val , $dim , $cDim+1 , ($slice . "[" . $i . "]") , $width );
//;         }      
//;         return $cfgStr ;
//;      }
//;      my @Ws = @W ;
//;      pop @Ws ;
//;     
//;       #Setup Reset Values if reset to zero or reset to one
//;       if (  $ResetToZero ) {
//;

               logic `$PS` rst_val`$US`;       
               `set( "rst_val" , $W[$#W]."'b0" , $Dim-1 , 0 , "" , \@Ws )` 
	              //assign rst_val = '0 ;

//;
//;       }
//;       if ( $ResetToOne ) {
//;

               logic `$PS` rst_val`$US`;
               `set( "rst_val" , $W[$#W]."1'b1" , $Dim-1 , 0 , "" , \@Ws )` 
               //assign rst_val = '1 ;

//;
//;       }
//;       if ( $Valid ) {
             logic data_en ;

//;          if ( $FlopValid ){
             logic q_valid_val ;
//;          }
			    
             assign data_en = en && d_valid ;

//;          if( $FlopValid ) {        
             assign q_valid = q_valid_val ;
               always @ (posedge clk or negedge rst ) begin  //Latches on RST or CLK
                  q_valid_val <= ~rst ? 1'b0 : ( en ? d_valid : q_valid ) ;   //'
               end
//;          }   

//;       } else {
             logic data_en ;
             assign data_en = en ;
//;       }
   
          logic `$PS` q_val`$US`;
          logic `$PS` d`$US`; 

          assign    d = din ; // Delay data in to prevent inf. loops
          assign    q = q_val ; 
   
          always @ (posedge clk or negedge rst ) begin  //Latches on RST or CLK
             q_val <= (!rst) ? rst_val : ( data_en ? d : q ) ;   
          end

//;   
//; } else {   
//;       $self->error("Unexpected Branch condition in pipeline elaboration PipelineDepth:$PipelineDepth\n" );
//; }

   
endmodule

