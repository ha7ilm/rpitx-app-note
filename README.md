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

These examples will use the raw audio file `music48000.raw` and `speech48000.raw`, which is present in this repo. You can get this file by:

    git clone https://github.com/ha7ilm/rpitx-app-note
    cd rpitx-app-note; ls  #There is your file.

A raw audio file differs from a .wav file because it doesn't have any headers to store its parameters, just the samples after each other.

**Generate AM modulation:**

    cat music48000.raw | csdr convert_i16_f | csdr gain_ff 1.0 | csdr dsb_fc | csdr add_dcoffset_cc | sudo rpitx -i- -m IQFLOAT -f 28400

* The part `csdr gain_ff 1.0` can be changed to increase/decrease modulation.
* The `-f 28400` at the end gives the transmit frequency in kHz.

**Generate USB modulation:**

    cat speech48000.raw | csdr convert_i16_f | csdr dsb_fc | csdr bandpass_fir_fft_cc 0 0.1 0.01 | csdr gain_ff 2.0 | csdr shift_addition_cc 0.2 | sudo rpitx -i- -m IQFLOAT -f 28400

**Generate LSB modulation:**

    cat speech48000.raw | csdr convert_i16_f | csdr dsb_fc | csdr bandpass_fir_fft_cc -0.1 0 0.01 | csdr gain_ff 2.0 | csdr shift_addition_cc 0.2 | sudo rpitx -i- -m IQFLOAT -f 28400

* It's the matter of the filter which sideband do we select: 
  * `csdr bandpass_fir_fft_cc -0.1 0` is the lower sideband
  * `csdr bandpass_fir_fft_cc 0 0.1` is the upper sideband
* I have experienced that if the SSB signal is in the center of the I/Q signal, then it is not transmitted correctly. The solution was to shift it. So the exact frequency should be around: `rpitx frequency + 48000*0.2 = rpitx frequency + 9600 Hz`

**Generate NFM modulation:**

    cat music48000.raw | csdr convert_i16_f | csdr gain_ff 7000 | csdr convert_f_samplerf 20833 | sudo rpitx -i- -m RF -f 28400

