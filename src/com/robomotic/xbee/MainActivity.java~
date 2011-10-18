/*
  XBeeIOIO.h - XBee interface for Android IOIO
  Copyright (c) 2011 Robomotic ltd.  All right reserved.
  
  This library is free software; you can redistribute it and/or
  modify it under the terms of the GNU Lesser General Public
  License as published by the Free Software Foundation; either
  version 2.1 of the License, or (at your option) any later version.

  This library is distributed in the hope that it will be useful,
  but WITHOUT ANY WARRANTY; without even the implied warranty of
  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
  Lesser General Public License for more details.

  You should have received a copy of the GNU Lesser General Public
  License along with this library; if not, write to the Free Software
  Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA

  Acknowledgements:
  This library is based on the Android IOIO library
  
*/

package com.robomotic.xbee;

import java.io.IOException;
import java.io.InputStream;


import ioio.lib.util.AbstractIOIOActivity;
import ioio.lib.util.AbstractIOIOTabActivity;
import ioio.lib.api.DigitalOutput;
import ioio.lib.api.Uart;
import ioio.lib.api.exception.ConnectionLostException;

import android.os.Bundle;
import android.util.Log;
import android.widget.TabHost;
import android.widget.TextView;



public class MainActivity extends AbstractIOIOTabActivity {

	private TabHost mTabHost;
	private Uart uart;
	private TextView output;
	private InputStream in;

	/**
	 * Called when the activity is first created. Here we normally initialize
	 * our GUI.
	 */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);

		mTabHost = getTabHost();

		mTabHost.addTab(mTabHost.newTabSpec("tab_control").setIndicator(
				"AT mode").setContent(R.id.tabATmode));
		mTabHost.addTab(mTabHost.newTabSpec("tab_settings").setIndicator(
				"API mode").setContent(R.id.tabAPImode));
		mTabHost.addTab(mTabHost.newTabSpec("tab_binding").setIndicator(
				"Settings").setContent(R.id.tabSettings));

		mTabHost.setCurrentTab(0);

		output= (TextView) findViewById(R.id.editTextSerial);
	}

	/**
	 * This is the thread on which all the IOIO activity happens. It will be run
	 * every time the application is resumed and aborted when it is paused. The
	 * method setup() will be called right after a connection with the IOIO has
	 * been established (which might happen several times!). Then, loop() will
	 * be called repetitively until the IOIO gets disconnected.
	 */
	class IOIOThread extends AbstractIOIOTabActivity.IOIOThread {
		/** The on-board LED. */
		Boolean imBusy = false;
		/** The on-board LED. */
		private DigitalOutput led_;
		private int bufferSize = 1000;

		/**
		 * Called every time a connection with IOIO has been established.
		 * Typically used to open pins.
		 * 
		 * @throws ConnectionLostException
		 *             When IOIO connection is lost.
		 * 
		 * @see ioio.lib.util.AbstractIOIOActivity.IOIOThread#setup()
		 */
		protected void setup() throws ConnectionLostException {
			try {
				led_ = ioio_.openDigitalOutput(0, true);
				uart = ioio_.openUart(4, 5,9600, Uart.Parity.NONE,
						Uart.StopBits.ONE);
				in = uart.getInputStream();
			} catch (ConnectionLostException e) {
				iWrite(e.getMessage());
				iWrite(Log.getStackTraceString(e));
			}
		}                                     

		@Override
		protected void loop() throws ConnectionLostException {
			led_.write(false);
			if (!imBusy) {
				imBusy = true;
				getMessage();
			}
		}

		private void getMessage() throws ConnectionLostException {

			try {
				int availableBytes = in.available();

				if (availableBytes > 0) {
					byte[] readBuffer = new byte[bufferSize];
					in.read(readBuffer, 0, availableBytes);
					char[] charstring= (new String(readBuffer, 0, availableBytes)).toCharArray();
					String message = new String(charstring);
					iWrite(message);
					led_.write(true);

				}

				sleep(500);
			} catch (InterruptedException e) {
				iWrite("Error: " + e);
			} catch (IOException e) {
				// TODO Auto-generated catch block
				// 
				iWrite("Error: " + e);
				e.printStackTrace();
			}
			imBusy = false;
		}

		private void iWrite(String myValue) {
			final String crossValue = myValue;
			runOnUiThread(new Runnable() {
				@Override
				public void run() {
					output.setText(output.getText() + crossValue + "\n");
				}
			});

		}

	}

	/**
	 * A method to create our IOIO thread.
	 * 
	 * @see ioio.lib.util.AbstractIOIOActivity#createIOIOThread()
	 */
	@Override
	protected AbstractIOIOTabActivity.IOIOThread createIOIOThread() {
		return new IOIOThread();
	}
}