# Application note on using rpitx

## Using with csdr to modulate streaming input

You will need the *dev* branch of *csdr* for doing this.<br />
Setup instructions:

   	git clone https://github.com/simonyiszk/csdr.git
   	cd csdr
   	git fetch
   	git checkout dev
   	make && sudo make install 

Note that it should be already done, if you installed *qtcsdr*.

### Modulate from raw audio file

Generate AM modulation:

    cat testsound48000.raw | csdr convert_i16_f | csdr gain_ff 1.0 | csdr dsb_fc | csdr add_dcoffset_cc | sudo rpitx -i- -m IQFLOAT -f 28400

* It will read the raw audio file `testsound48000.raw`, which is present in this repo. You can get this file by:

    git clone https://github.com/ha7ilm/rpitx-app-note
	cd rpitx-app-note; ls  #There is your file.

* The part `csdr gain_ff 1.0` can be changed to increase/decrease modulation.
* The `-f 28400` at the end gives the transmit frequency in kHz.




    
