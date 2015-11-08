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

We will play these files in a loop, you can stop it with Ctrl+C.

**Generate AM modulation:**

    (while true; do cat music48000.raw; done) | csdr convert_i16_f | csdr gain_ff 1.0 | csdr dsb_fc | csdr add_dcoffset_cc | sudo rpitx -i- -m IQFLOAT -f 28400

* The part `csdr gain_ff 1.0` can be changed to increase/decrease modulation.
* The `-f 28400` at the end gives the transmit frequency in kHz.

**Generate USB modulation:**

    (while true; do cat speech48000.raw; done) | csdr convert_i16_f | csdr dsb_fc | csdr bandpass_fir_fft_cc 0 0.1 0.01 | csdr gain_ff 2.0 | csdr shift_addition_cc 0.2 | sudo rpitx -i- -m IQFLOAT -f 28400

**Generate LSB modulation:**

    (while true; do cat speech48000.raw; done) | csdr convert_i16_f | csdr dsb_fc | csdr bandpass_fir_fft_cc -0.1 0 0.01 | csdr gain_ff 2.0 | csdr shift_addition_cc 0.2 | sudo rpitx -i- -m IQFLOAT -f 28400

* It's the matter of the filter which sideband do we select: 
  * `csdr bandpass_fir_fft_cc -0.1 0` is the lower sideband
  * `csdr bandpass_fir_fft_cc 0 0.1` is the upper sideband
* I have experienced that if the SSB signal is in the center of the I/Q signal, then it is not transmitted correctly. The solution was to shift it. So the exact frequency should be around: `rpitx frequency + 48000*0.2 = rpitx frequency + 9600 Hz`

**Generate NFM modulation:**

    (while true; do cat music48000.raw; done) | csdr convert_i16_f | csdr gain_ff 7000 | csdr convert_f_samplerf 20833 | sudo rpitx -i- -m RF -f 28400

**Generate WFM modulation:**

    (while true; do cat music48000.raw; done) | csdr convert_i16_f | csdr gain_ff 70000 | csdr convert_f_samplerf 20833 | sudo rpitx -i- -m RF -f 28400

* Frequency deviation is set much higher with `csdr gain_ff 70000`, that's the only difference between this and NFM.
