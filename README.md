<form>
  <label>Hardware and Software Details: Linux Servers</label>
  <fieldset submitButton="false">
    <input type="dropdown" token="host">
      <label>Server</label>
      <prefix>host="</prefix>
      <suffix>"</suffix>
      <fieldForLabel>host</fieldForLabel>
      <fieldForValue>host</fieldForValue>
      <search>
        <query>
          index=os sourcetype=hardware 
          | eval host=upper(host) 
          | dedup host 
          | sort host 
          | table host
        </query>
      </search>
    </input>
  </fieldset>
  <row>
    <panel>
      <single>
        <title>HostName</title>
        <search>
          <query>
            index=os sourcetype=hardware $host$ 
            | eval host=upper(host)
            | dedup host 
            | table host
          </query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
      </single>
    </panel>
      <single>
        <title>Description</title>
        <search>
          <query>
            index=os sourcetype=hardware $host$
            | eval host=upper(host)
            | lookup Perimeter.csv Hostname AS host OUTPUT Description 
            | table Description
          </query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
      </single>
    </panel>
  </row>
  <row>
    <panel>
      <title>Hardware</title>
      <table>
        <search>
          <query>
            index=os sourcetype=hardware $host$
            | dedup host 
            | eval MEMORY_REAL=MEMORY_REAL/1024/1024, MEMORY_SWAP=MEMORY_SWAP/1024/1024, host=upper(host)
            | table CPU_TYPE CPU_COUNT CPU_CACHE MEMORY_REAL MEMORY_SWAP fd0 hdc sda 
            | rename CPU_TYPE AS CPU CPU_COUNT AS "Number of CPUs" CPU_CACHE AS Cache MEMORY_REAL As RAM MEMORY_SWAP AS Swap HARD_DRIVES AS "Hard Disks" fd0 AS "Floppy Disk" hdc AS "Hard Disk" sda AS "Virtual disk"
          </query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">100</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
    <panel>
      <title>Operative System</title>
      <table>
        <search>
          <query>
            index=os sourcetype=Unix:Version $host$
            | table os_name os_release os_version machine_architecture_name
            | rename os_name AS "Operative System" os_release AS Release os_version AS Version machine_architecture_name AS Architecture
          </query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">100</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>df</title>
      <table>
        <search>
          <query>
            index=os  sourcetype=df $host$ 
            | dedup host 
            | multikv 
            | table Filesystem Type Size Used Avail UsePct MountedOn
          </query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">100</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>Processes</title>
      <table>
        <search>
          <query>index=os sourcetype=ps $host$ 
            | multikv 
            | table USER PID PSR pctCPU CPUTIME pctMEM RSZ_KB VSZ_KB TTY S ELAPSED COMMAND ARGS</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">10</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>netstat</title>
      <table>
        <search>
          <query>index=os sourcetype=netstat $host$ 
            | dedup host 
            | multikv 
            | table Proto Recv-Q Send-Q LocalAddress ForeignAddress State</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">10</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
    <panel>
      <title>packages</title>
      <table>
        <search>
          <query>index=os sourcetype=package $host$ 
            | multikv 
            | dedup host NAME 
            | table NAME VERSION RELEASE ARCH VENDOR GROUP 
            | sort NAME</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">10</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>top command</title>
      <table>
        <search>
          <query>index=os sourcetype=top $host$ 
            | dedup host 
            | multikv 
            | table PID USER PR NI VIRT RES SHR S pctCPU pctMEM cpuTIME COMMAND</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">10</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>protocol</title>
      <table>
        <search>
          <query>index=os sourcetype=protocol $host$ 
            | dedup host 
            | multikv 
            | table IPdropped TCPrexmits TCPreorder TCPpktRecv TCPpktSent UDPpktLost UDPunkPort UDPpktRecv UDPpktSent</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">10</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>openPorts</title>
      <table>
        <search>
          <query>index=os sourcetype=openPorts $host$ 
            | dedup host 
            | multikv 
            | table Proto Port</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">10</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
    <panel>
      <title>Users with private logins</title>
      <table>
        <search>
          <query>index=os sourcetype=usersWithLoginPrivs $host$ 
            | dedup host 
            | multikv 
            | table USERNAME HOME_DIR USER_INFO</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
          <sampleRatio>1</sampleRatio>
        </search>
        <option name="count">100</option>
        <option name="dataOverlayMode">none</option>
        <option name="drilldown">cell</option>
        <option name="percentagesRow">false</option>
        <option name="rowNumbers">false</option>
        <option name="totalsRow">false</option>
        <option name="wrap">true</option>
        <format type="number" field="Floppy Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Hard Disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Virtual disk">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="RAM">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Swap">
          <option name="unit">GB</option>
        </format>
        <format type="number" field="Cache">
          <option name="unit">kB</option>
        </format>
      </table>
    </panel>
  </row>
</form>
