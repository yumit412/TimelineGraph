<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  // Default blueprint mock data
  const DEFAULT_DATA = `// SYSTEM OPERATION TIMELINE
// FORMAT: EQUIPMENT | YYYY-MM-DD | TITLE | DESCRIPTION
#Unit 1 | 2026-06-12 | Wiring Change Verification | No malfunction occurred after wiring change (H/W Safety function works properly)

#Unit 2 | 2026-06-01 | Switch Off Stop Failure | Failed to stop movement when Console power switch was turned Off during operation (H/W Safety not working, only F/W Safety active)

#Unit 3 | 2025-02-04 | Assembly Completed | Assembly completed
#Unit 3 | 2025-12-09 | Install F/W 2.0.0 | F/W 2.0.0 installed (E-Stop function improved, AC-DC adapter traceability secured)
#Unit 3 | 2025-12-24 | Rollback F/W 1.1.1 | F/W 1.1.1 rollback installation
#Unit 3 | 2025-12-29 | Install F/W 2.1.2 | F/W 2.1.2 installed (EVR System Model 3R movement/usability improved, passed SQA)
#Unit 3 | 2026-02-28 | Install F/W 2.1.3 | F/W 2.1.3 installed (Resolved freeze during MC_F and GW_M modules retract, Collet Grab improved)

#Unit 4 | 2025-03-21 | Assembly Completed | Assembly completed
#Unit 4 | 2025-08-20 | Install F/W 2.0.0 | F/W 2.0.0 installed (E-Stop function improved, AC-DC adapter traceability secured)
#Unit 4 | 2025-12-12 | Install F/W 2.1.2 | F/W 2.1.2 installed (EVR System Model 3R movement/usability improved, passed SQA)
#Unit 4 | 2026-05-22 | Install F/W 2.1.3 | F/W 2.1.3 installed (Resolved freeze during MC_F and GW_M modules retract, Collet Grab improved)
#Unit 4 | 2026-06-01 | Switch Off Stop Failure | Failed to stop movement when Console power switch was turned Off during operation (H/W Safety not working, only F/W Safety active)

#Common | 2026-06-01 | Cable Removal Test | Verified normal stop upon Interface Cable removal (F/W Safety working normally)

#Unit 2 | 2026-01-30 | NCR-K000064 | Control Console #2 Rework
#Unit 2 | 2024-12-10 | NCR-K000020 | EV240#2 Manufacturing Non-conformance
#Unit 3 | 2026-06-04 | NCR-K000068 | EVR Unit #3 – Slow Module Movement During 1 mm Command
#Unit 3 | 2026-06-02 | NCR-K000067 | EVR Unit 3 System Cart Rework
#Unit 3 | 2025-12-05 | NCR-K000058 | EVR Unit 3 and Unit 4 System Cart Rework
#Unit 4 | 2026-05-27 | NCR-K000066 | EVR Unit 4 Abnormal Behavior
#Unit 4 | 2026-01-21 | NCR-K000063 | EVR Unit 4 Bedside Unit Investigation and Remote Console Rework
#Unit 4 | 2025-12-05 | NCR-K000058 | EVR Unit 3 and Unit 4 System Cart Rework
#Unit 4 | 2025-09-10 | NCR-K000055 | EVR Unit 4 Overcurrent
#Unit 4 | 2025-05-22 | NCR-K000046 | EV240 #4 CAN Communication Failure due to NCR-K000045 rework
#Common | 2026-04-29 | NCR-K000065 | Motor Power Loss
#Common | 2026-01-06 | NCR-K000062 | TP-K0017 Model 3R EP Execution Non-conformances
#Common | 2025-12-24 | NCR-K000061 | Non-conformance of IC240 Cassettes
#Common | 2025-12-22 | NCR-K000060 | Sterilized Inventory EO GAS Chemical Indicator Inconsistency
#Common | 2025-12-05 | NCR-K000059 | Labeling Rework
#Common | 2025-11-17 | NCR-K000057 | [EVS240R] DVM Module No.4 Translation-Area Deviation and Overcurrent Issue
#Common | 2025-11-07 | NCR-K000056 | E-Stop Inconsistency
#Common | 2025-09-02 | NCR-K000054 | Adapter Rework
#Common | 2025-08-19 | NCR-K000053 | Model 3R 60601-1-2 Debugging
#Common | 2025-08-11 | NCR-K000052 | Conversion from Model 3 to Model 3R
#Common | 2025-08-06 | NCR-K000051 | EV240 unit #4 SW Validation Failure Debugging
#Common | 2025-08-05 | NCR-K000050 | System Cart Rework
#Common | 2025-07-10 | NCR-K000049 | Positioning Arm LED Strip Non-conformance
#Common | 2025-06-24 | NCR-K000048 | Adaptor Identification and Rework
#Common | 2025-06-24 | NCR-K000047 | 3R Control Console Rework - Rubber Stopper
#Common | 2025-05-14 | NCR-K000045 | Positioning Arm Color-coded Sockets Non-conformance
#Common | 2025-04-18 | NCR-K000044 | Replacement of a potentially damaged joystick in CST Controller before conducting the IEC 60601-1 test
#Common | 2025-04-12 | CAR-000026 | Internal Audit Finding #1: Rework
#Common | 2025-04-08 | NCR-K000043 | Usability Issues during Model Testing
#Common | 2025-04-08 | NCR-K000042 | Hemocompatibility Testing Failure
#Common | 2025-04-08 | NCR-K000041 | Single Batch Sterilization
#Common | 2025-04-07 | NCR-K000040 | 3R Console Reworks - 60601
#Common | 2025-04-04 | NCR-K000039 | Board Supplier Rework
#Common | 2025-03-18 | NCR-K000038 | 24AE0030002A-10MAR25-0002 Part Non-conformance
#Common | 2025-03-18 | NCR-K000037 | EV240 rework to match 60601-1-2 debugging
#Common | 2025-03-11 | NCR-K000036 | Board Supplier Rework
#Common | 2025-03-11 | NCR-K000035 | EVR Gen2.4 CST Controller Board Incoming Inspection Non-conformance
#Common | 2025-03-07 | NCR-K000034 | Cassette Parts Manufacturing Non-Confromance
#Common | 2025-02-07 | NCR-K000033 | EV240 60601-1-2 Debugging
#Common | 2025-02-06 | NCR-K000032 | Supplier Manufacturing Error: 24AM0231004A
#Common | 2025-02-04 | NCR-K000031 | EVR240 60601-1-2 Debugging
#Common | 2025-02-04 | NCR-K000030 | Loctite 401 Storage Temperature Non-conformance
#Common | 2025-01-20 | NCR-K000029 | 3R CTRL Console fastener length insufficiency
#Common | 2025-01-20 | NCR-K000028 | 3R CTRL Console parts supplier manufacturing error
#Common | 2025-01-10 | NCR-K000027 | Buckling Detection System Distance Recalibration
#Common | 2025-01-03 | NCR-K000026 | R&D Cassette Build Conversion
#Common | 2024-12-30 | NCR-K000025 | IO Testing Finds
#Common | 2024-12-27 | NCR-K000023 | Slide Lock Pin and Base Holes
#Common | 2024-12-19 | NCR-K000022 | Cassette Lock Block Non-conformance
#Common | 2024-12-18 | NCR-K000021 | Collet Block GW Nonconformance
#Common | 2024-12-10 | NCR-K000019 | Single-Use Components Incoming Inspection Non-conformances
#Common | 2024-12-09 | NCR-K000018 | Single Use Cassette Manufacturing Deviation
#Common | 2024-12-09 | NCR-K000017 | GW Rotate Master Gear Nonconformance
#Common | 2024-11-08 | NCR-K000016 | Single-Use Components Incoming Inspection Non-conformances
#Common | 2024-10-29 | NCR-K000015 | EV240 for IEC 60601-1 Testing
#Common | 2024-10-29 | NCR-K000014 | Loctite-242 Storage Temperature Requirement Discrepancy
#Common | 2024-10-23 | NCR-K000013 | EV240 Bedside Unit Break Switch Malfunction
#Common | 2024-10-22 | NCR-K000012 | EV240 Control Console IO Testing Failure
#Common | 2024-10-21 | NCR-K000011 | Clamp parts supplier manufacturing error
#Common | 2024-10-21 | NCR-K000010 | Clamp part incoming inspection non-conformance
#Common | 2024-10-21 | NCR-K000009 | Clamp parts incoming inspection failure due to drawing errors
#Common | 2024-10-21 | NCR-K000008 | 24AM0134014A M8 SCREW Supplier Inspection Data Absence
#Common | 2024-10-17 | NCR-K000007 | MP-K0001 Assembly(Harness)
#Common | 2024-10-17 | NCR-K000006 | MP-K0001 Mechanical Assembly Non-conformance
#Common | 2024-10-16 | NCR-K000005 | Loctite 401 Storage Temperature Non-conformance
#Common | 2024-10-16 | NCR-K000004 | 24AM0131234A Motor BL-1 Supplier Inspection Data Absence
#Common | 2024-09-26 | CAR-K000001 | Incoming Inspection Process Deviation
#Common | 2024-09-02 | NCR-K000003 | Electrical Off-the-Shelf parts incoming inspection nonconformance
#Common | 2024-08-30 | NCR-K000002 | Supplier Inspection Data Absence
#Common | 2024-08-29 | NCR-K000001 | Supplier Inspection Data Resolution Insufficiency`;

  // Reactive State (Svelte 5 Runes)
  let rawText = $state('');
  let events = $state([]);
  let balloonWidth = $state(180);
  let balloonHeight = $state(70);
  let collisionGap = $state(20);
  let leaderStyle = $state('orthogonal'); // 'orthogonal' or 'diagonal'
  let activeTab = $state('text'); // 'text' or 'table'
  let isEmbedded = $state(false);
  let windowWidth = $state(1200);
  
  let consoleMsg = $state({ text: 'READY. Waiting for input.', isError: false });
  let showToast = $state(false);
  let hoveredEventId = $state(null);
  let searchQuery = $state('');
  let hiddenEquipments = $state([]);
  let showVisibilityPopover = $state(false);
  let selectedEventId = $state(null);
  let hiddenEventTitles = $state([]);

  function hideSameBalloons(title) {
    if (!hiddenEventTitles.includes(title)) {
      hiddenEventTitles = [...hiddenEventTitles, title];
    }
    selectedEventId = null;
  }

  function showAllHiddenBalloons() {
    hiddenEventTitles = [];
  }

  const availableEquipments = $derived.by(() => {
    const names = [...new Set(events.map(e => e.equipment || 'UNASSIGNED'))];
    return names.sort();
  });

  function toggleEquipmentVisibility(eqName) {
    if (hiddenEquipments.includes(eqName)) {
      hiddenEquipments = hiddenEquipments.filter(name => name !== eqName);
    } else {
      hiddenEquipments = [...hiddenEquipments, eqName];
    }
  }

  function isHighlighted(event) {
    if (!searchQuery.trim()) return false;
    const q = searchQuery.toLowerCase().trim();
    return (event.title || '').toLowerCase().includes(q) || 
           (event.desc || '').toLowerCase().includes(q) ||
           (event.equipment || '').toLowerCase().includes(q) ||
           (event.date || '').toLowerCase().includes(q);
  }

  const hoveredEvent = $derived.by(() => {
    if (!hoveredEventId) return null;
    return processedTimelineData.flatMap(r => r.events).find(e => e.id === hoveredEventId) || null;
  });

  function isHovered(event) {
    if (!hoveredEventId) return false;
    if (event.id === hoveredEventId) return true;
    const hEvent = hoveredEvent;
    return hEvent && event.title === hEvent.title;
  }

  // D3 Zooming & Sizing State
  let svgElement = $state(null);
  let zoomTransform = $state(d3.zoomIdentity);
  let zoomBehavior;

  // Header current date
  const currentDate = new Date().toISOString().split('T')[0];

  // URL Data Compression Utilities (70% size reduction, backward compatible)
  async function compressText(text) {
    if (typeof CompressionStream === 'undefined') {
      return btoa(unescape(encodeURIComponent(text)));
    }
    try {
      const stream = new Blob([text]).stream();
      const compressedStream = stream.pipeThrough(new CompressionStream('deflate'));
      const buffer = await new Response(compressedStream).arrayBuffer();
      const bytes = new Uint8Array(buffer);
      let binary = '';
      for (let i = 0; i < bytes.byteLength; i++) {
        binary += String.fromCharCode(bytes[i]);
      }
      return 'z/' + btoa(binary);
    } catch (e) {
      console.error('Compression failed, falling back to standard base64:', e);
      return btoa(unescape(encodeURIComponent(text)));
    }
  }

  async function decompressText(hash) {
    if (hash.startsWith('z/')) {
      try {
        const base64Data = hash.substring(2);
        const binary = atob(base64Data);
        const bytes = new Uint8Array(binary.length);
        for (let i = 0; i < binary.length; i++) {
          bytes[i] = binary.charCodeAt(i);
        }
        const stream = new Blob([bytes]).stream();
        const decompressedStream = stream.pipeThrough(new DecompressionStream('deflate'));
        return await new Response(decompressedStream).text();
      } catch (e) {
        console.error('Decompression failed:', e);
        throw e;
      }
    } else {
      // Legacy uncompressed base64 hash fallback
      const sanitizedHash = hash.replace(/[^A-Za-z0-9\+\/\=]/g, '');
      return decodeURIComponent(escape(atob(sanitizedHash)));
    }
  }

  // Load from URL Hash or Fallback to Default on mount
  onMount(() => {
    // Check URL parameters for embedded view mode
    const urlParams = new URLSearchParams(window.location.search);
    isEmbedded = urlParams.get('embed') === 'true';

    // Attach resize listener to scale timeline baseline to fit width dynamically
    const handleResize = () => {
      if (svgElement) {
        windowWidth = svgElement.clientWidth || window.innerWidth;
      }
    };
    window.addEventListener('resize', handleResize);
    handleResize();

    // Initialize D3 Zoom Behavior
    if (svgElement) {
      zoomBehavior = d3.zoom()
        .scaleExtent([0.1, 5])
        .on('zoom', (event) => {
          zoomTransform = event.transform;
        });

      d3.select(svgElement).call(zoomBehavior);
    }

    const hash = window.location.hash.substring(1);
    if (hash) {
      decompressText(hash).then(decoded => {
        if (decoded && decoded.trim().length > 0) {
          rawText = decoded;
          parseRawText();
        } else {
          loadDefault();
        }
      }).catch(err => {
        console.error('Failed to decode hash data:', err);
        consoleMsg = { text: 'ERROR: Corrupted URL data hash. Fallback to default.', isError: true };
        loadDefault();
      }).finally(() => {
        // Trigger initial fit-to-screen view
        setTimeout(zoomToFit, 150);
      });
    } else {
      loadDefault();
      // Trigger initial fit-to-screen view
      setTimeout(zoomToFit, 150);
    }

    return () => {
      window.removeEventListener('resize', handleResize);
    };
  });

  // Automatically update the URL hash whenever rawText changes to maintain shareability
  $effect(() => {
    if (rawText) {
      compressText(rawText).then(encoded => {
        window.history.replaceState(null, '', '#' + encoded);
      }).catch(e => {
        console.error('Hash serialization failed:', e);
      });
    } else {
      window.history.replaceState(null, '', window.location.pathname);
    }
  });

  function loadDefault() {
    rawText = DEFAULT_DATA;
    parseRawText();
  }

  /* ==========================================================================
     DATA PARSING & SYNCHRONIZATION
     ========================================================================== */

  // Parse raw text into structured events state
  function parseRawText() {
    const lines = rawText.split('\n');
    const parsedEvents = [];
    let errorCount = 0;
    let parsedCount = 0;

    lines.forEach((line) => {
      const trimmed = line.trim();
      if (!trimmed || trimmed.startsWith('//')) {
        return;
      }

      const parts = trimmed.split('|');
      if (parts.length < 3) {
        errorCount++;
        return;
      }

      const equipment = parts[0].trim();
      const dateStr = parts[1].trim();
      const title = parts[2].trim();
      const desc = parts.length > 3 ? parts[3].trim() : '';

      // Date Format validation: YYYY-MM-DD
      const dateRegex = /^\d{4}-\d{2}-\d{2}$/;
      if (!dateRegex.test(dateStr) || isNaN(Date.parse(dateStr))) {
        errorCount++;
        return;
      }

      parsedEvents.push({
        id: `ev-${Math.random().toString(36).substring(2, 9)}`,
        equipment: equipment || 'UNASSIGNED',
        date: dateStr,
        title: title || 'UNTITLED',
        desc: desc
      });
      parsedCount++;
    });

    events = parsedEvents;
    if (errorCount > 0) {
      consoleMsg = {
        text: `WARNING: Parsed ${parsedCount} events. Ignored ${errorCount} lines with incorrect formatting.`,
        isError: true
      };
    } else {
      consoleMsg = {
        text: `SUCCESS: Synced ${parsedCount} components. Visual matrix updated.`,
        isError: false
      };
    }
  }

  // Update Raw Text Editor based on BOM Form modification
  function syncTextFromEvents() {
    let text = `// SYSTEM OPERATION TIMELINE\n// FORMAT: EQUIPMENT | YYYY-MM-DD | TITLE | DESCRIPTION\n`;
    
    // Group events for formatting hygiene
    const groups = {};
    events.forEach(e => {
      if (!groups[e.equipment]) groups[e.equipment] = [];
      groups[e.equipment].push(e);
    });

    for (const [eq, list] of Object.entries(groups)) {
      list.forEach(e => {
        text += `${e.equipment} | ${e.date} | ${e.title} | ${e.desc || ''}\n`;
      });
      text += `\n`;
    }
    rawText = text.trim();
    
    consoleMsg = {
      text: `BOM SHEET UPDATED: Synced ${events.length} elements.`,
      isError: false
    };
  }

  // Handle live form inputs
  function onBomInput() {
    syncTextFromEvents();
  }

  function addBomRow() {
    events.push({
      id: `ev-${Math.random().toString(36).substring(2, 9)}`,
      equipment: events.length > 0 ? events[events.length - 1].equipment : 'SYS-UNIT-01',
      date: new Date().toISOString().split('T')[0],
      title: 'NEW_EVENT',
      desc: 'Description log'
    });
    events = [...events];
    syncTextFromEvents();
  }

  function deleteBomRow(index) {
    events.splice(index, 1);
    events = [...events];
    syncTextFromEvents();
  }

  function clearAllEvents() {
    if (confirm('ARE YOU SURE YOU WANT TO DESTRUCT ALL GEOMETRIES?')) {
      events = [];
      rawText = '';
      consoleMsg = { text: 'SYSTEM ERASURE COMPLETE. VIEWPORT CLEARED.', isError: true };
    }
  }

  /* ==========================================================================
     TIMELINE GEOMETRY GRAPH COMPUTATION
     ========================================================================== */

  // Dynamic balloon height calculator based on text content wrapping
  function calculateEventHeight(title, desc, width, minHeight) {
    const charsPerLine = Math.max(15, Math.floor((width - 12) / 5.5));
    let lineCount = 0;
    if (desc) {
      const lines = desc.split('\n');
      lines.forEach(l => {
        if (l.trim() === '') {
          lineCount += 1;
        } else {
          lineCount += Math.max(1, Math.ceil(l.length / charsPerLine));
        }
      });
    }

    const headerHeight = 24;
    const descPadding = 12;
    const lineHeight = 12;
    const actionsFooterHeight = 23; // Padding for action buttons inside the expanded balloon
    
    let calculatedHeight = headerHeight + actionsFooterHeight;
    if (lineCount > 0) {
      calculatedHeight += descPadding + lineCount * lineHeight;
    } else {
      calculatedHeight += 8;
    }
    
    return Math.max(minHeight, Math.min(170, calculatedHeight));
  }

  // Re-calculate layout positions with collision avoidance whenever parameters or data state shifts
  const processedTimelineData = $derived.by(() => {
    if (events.length === 0) return [];

    // Filter valid dates, sort, and exclude hidden balloon titles
    const sorted = [...events]
      .filter(e => e.date && !isNaN(Date.parse(e.date)))
      .filter(e => !hiddenEventTitles.includes(e.title))
      .map(e => ({
        ...e,
        time: new Date(e.date).getTime()
      }))
      .sort((a, b) => a.time - b.time);

    if (sorted.length === 0) return [];

    // Group by equipment
    const groups = {};
    sorted.forEach(e => {
      if (!groups[e.equipment]) {
        groups[e.equipment] = [];
      }
      groups[e.equipment].push(e);
    });

    const visibleEqNames = Object.keys(groups).filter(name => !hiddenEquipments.includes(name));
    const minTime = sorted[0].time;
    const maxTime = sorted[sorted.length - 1].time;
    const timeSpan = Math.max(maxTime - minTime, 24 * 60 * 60 * 1000);

    const leftMargin = 180;
    const timelineWidth = Math.max(600, windowWidth - leftMargin - 80);

    // Use d3 to scale the time domain to coordinate range
    const xScale = d3.scaleTime()
      .domain([new Date(minTime), new Date(maxTime)])
      .range([leftMargin, leftMargin + timelineWidth]);

    const rowHeight = 190;
    const Y_steps = [-1, 1, -2, 2, -3, 3]; // Vertical offset layers: Top 1st, Bottom 1st, Top 2nd, etc.

    return visibleEqNames.map((eqName, eqIdx) => {
      const Y_axis = 180 + eqIdx * rowHeight;
      const groupEvents = groups[eqName];
      const placedBoxes = [];

      const renderedEvents = groupEvents.map((event, evIdx) => {
        const xOrig = xScale(new Date(event.date));
        let xBalloon = xOrig;
        let yBalloon = 0;
        let selectedStep = -1;
        let placed = false;

        // Calculate height: compact 34px if not selected, otherwise full expanded height
        const isSelected = selectedEventId === event.id;
        const H = isSelected 
          ? calculateEventHeight(event.title, event.desc, balloonWidth, balloonHeight) 
          : 34;

        // Shift X coordinate incrementally if collision persists at all layers
        const xShiftDirections = [0, 20, -20, 40, -40, 60, -60, 80, -80, 100, -100, 120, -120, 150, -150, 180, -180, 210, -210];

        for (let shift of xShiftDirections) {
          const candidateX = xOrig + shift;

          for (let step of Y_steps) {
            selectedStep = step;
            let boxY1 = 0;
            let boxY2 = 0;

            const layerSpacing = Math.max(balloonHeight, 80) + 12;

            if (step < 0) {
              // Balloon placed ABOVE timeline axis
              const multiplier = Math.abs(step) - 1;
              boxY2 = Y_axis - 22 - multiplier * layerSpacing;
              boxY1 = boxY2 - H;
            } else {
              // Balloon placed BELOW timeline axis
              const multiplier = step - 1;
              boxY1 = Y_axis + 22 + multiplier * layerSpacing;
              boxY2 = boxY1 + H;
            }

            // Create hypothetical collision bounding box
            const box = {
              x1: candidateX - balloonWidth / 2 - collisionGap / 2,
              x2: candidateX + balloonWidth / 2 + collisionGap / 2,
              y1: boxY1 - collisionGap / 2,
              y2: boxY2 + collisionGap / 2
            };

            // Evaluate intersection with existing nodes
            let collides = false;
            for (let placedBox of placedBoxes) {
              if (box.x1 < placedBox.x2 &&
                  box.x2 > placedBox.x1 &&
                  box.y1 < placedBox.y2 &&
                  box.y2 > placedBox.y1) {
                collides = true;
                break;
              }
            }

            if (!collides) {
              xBalloon = candidateX;
              yBalloon = boxY1;
              placedBoxes.push({
                x1: candidateX - balloonWidth / 2,
                x2: candidateX + balloonWidth / 2,
                y1: boxY1,
                y2: boxY2
              });
              placed = true;
              break;
            }
          }
          if (placed) break;
        }

        // Setup leader connection lines
        const balloonAttachY = (selectedStep < 0) ? yBalloon + H : yBalloon;
        let pathData = '';

        if (leaderStyle === 'orthogonal') {
          // CAD-style 90-degree leader line bends
          const yOffset = (selectedStep < 0) ? -11 : 11;
          const bendY = Y_axis + yOffset;
          pathData = `M ${xOrig} ${Y_axis} ` +
                     `L ${xOrig} ${bendY} ` +
                     `L ${xBalloon} ${bendY} ` +
                     `L ${xBalloon} ${balloonAttachY}`;
        } else {
          // Standard diagonal connection line
          pathData = `M ${xOrig} ${Y_axis} L ${xBalloon} ${balloonAttachY}`;
        }

        const theme = getEventTheme(event.title);
        return {
          ...event,
          xOrig,
          xBalloon,
          yBalloon,
          selectedStep,
          pathData,
          theme,
          H
        };
      });

      const eqTheme = getEqTheme(eqName);
      return {
        eqName,
        Y_axis,
        startX: leftMargin,
        endX: leftMargin + timelineWidth,
        separatorEndX: leftMargin + timelineWidth + 70,
        eqTheme,
        events: renderedEvents
      };
    });
  });

  // Calculate ticks to display on the background timeline ruler
  const timelineTicks = $derived.by(() => {
    if (events.length === 0) return [];
    const sorted = [...events]
      .filter(e => e.date && !isNaN(Date.parse(e.date)))
      .map(e => new Date(e.date).getTime())
      .sort((a, b) => a - b);

    if (sorted.length === 0) return [];
    
    const minTime = sorted[0];
    const maxTime = sorted[sorted.length - 1];
    
    const minDate = new Date(minTime);
    // Add 1 day padding
    const maxDate = new Date(maxTime + 24 * 60 * 60 * 1000);
    const timeSpan = Math.max(maxTime - minTime, 24 * 60 * 60 * 1000);

    const leftMargin = 180;
    const timelineWidth = Math.max(600, windowWidth - leftMargin - 80);

    const getX = (t) => {
      const ratio = (t - minTime) / timeSpan;
      return leftMargin + ratio * timelineWidth;
    };

    const diffDays = Math.ceil((maxTime - minTime) / (1000 * 60 * 60 * 24));
    let step = 1;
    if (diffDays > 365) {
      step = 60; // 2 months spacing
    } else if (diffDays > 180) {
      step = 30; // 1 month spacing
    } else if (diffDays > 90) {
      step = 15; // 15 days spacing
    } else if (diffDays > 30) {
      step = 7;  // 7 days spacing
    } else if (diffDays > 10) {
      step = 3;  // 3 days spacing
    }

    try {
      const rawTicks = d3.timeDay.range(minDate, maxDate, step);
      return rawTicks.map(d => ({
        dateStr: d.toISOString().split('T')[0].substring(5), // MM-DD
        x: getX(d.getTime())
      }));
    } catch (err) {
      console.error('Failed to generate D3 ticks:', err);
      return [];
    }
  });

  /* ==========================================================================
     D3 VIEWPORT VIEW NAVIGATION
     ========================================================================== */

  function zoomIn() {
    if (svgElement && zoomBehavior) {
      d3.select(svgElement).transition().duration(150).call(zoomBehavior.scaleBy, 1.25);
    }
  }

  function zoomOut() {
    if (svgElement && zoomBehavior) {
      d3.select(svgElement).transition().duration(150).call(zoomBehavior.scaleBy, 0.8);
    }
  }

  function zoomReset() {
    if (svgElement && zoomBehavior) {
      d3.select(svgElement).transition().duration(200).call(zoomBehavior.transform, d3.zoomIdentity);
    }
  }

  function zoomToFit() {
    if (!svgElement || !zoomBehavior) return;
    const svgGroup = svgElement.querySelector('#timeline-content');
    if (!svgGroup) return;
    const bbox = svgGroup.getBBox();
    if (bbox.width === 0 || bbox.height === 0) return;

    const width = svgElement.clientWidth || 800;
    const height = svgElement.clientHeight || 600;
    const padding = 80;

    const scale = Math.min(
      (width - padding * 2) / bbox.width,
      (height - padding * 2) / bbox.height,
      1.1
    );

    const tx = (width - bbox.width * scale) / 2 - bbox.x * scale;
    const ty = (height - bbox.height * scale) / 2 - bbox.y * scale;

    d3.select(svgElement).transition().duration(250).call(
      zoomBehavior.transform,
      d3.zoomIdentity.translate(tx, ty).scale(scale)
    );
  }

  // CAD engineering color scheme mapping based on event title string hashing
  function getEventTheme(title) {
    const t = (title || '').trim();
    if (!t) {
      return { bg: '#FFFFFF', stroke: '#202020', text: '#505050' };
    }

    // 10 contrasting muted engineering drawing palettes
    const COLOR_PALETTE = [
      { bg: '#E8F0FE', stroke: '#1A73E8', text: '#1A73E8' }, // Engineering Blue
      { bg: '#E8F5E9', stroke: '#2E7D32', text: '#2E7D32' }, // Muted Sage Green
      { bg: '#FFF3E0', stroke: '#E65100', text: '#E65100' }, // Muted Amber
      { bg: '#FEEBEE', stroke: '#C62828', text: '#C62828' }, // Muted Red
      { bg: '#F3E5F5', stroke: '#7B1FA2', text: '#7B1FA2' }, // Muted Purple
      { bg: '#E0F7FA', stroke: '#006064', text: '#006064' }, // Muted Teal
      { bg: '#FFFDE7', stroke: '#F57F17', text: '#F57F17' }, // Muted Olive
      { bg: '#EFEBE9', stroke: '#4E342E', text: '#4E342E' }, // Muted Earth Brown
      { bg: '#ECEFF1', stroke: '#546E7A', text: '#37474F' }, // Steel Charcoal
      { bg: '#F5F5F5', stroke: '#616161', text: '#212121' }  // Neutral Slate Gray
    ];

    // Compute standard djb2 hash code for string identity
    let hash = 5381;
    for (let i = 0; i < t.length; i++) {
      hash = ((hash << 5) + hash) + t.charCodeAt(i);
    }

    const index = Math.abs(hash) % COLOR_PALETTE.length;
    return COLOR_PALETTE[index];
  }

  // CAD engineering equipment Title Block coloring logic (Charcoal, Teal, Indigo, Muted Navy)
  function getEqTheme(eqName) {
    // Return unified clean CAD neutral specs
    return { bg: '#f8f8f8', stroke: '#505050', text: '#202020' };
  }

  /* ==========================================================================
     SOCIAL UTILITIES & SVG STANDALONE DOWNLOAD
     ========================================================================== */

  function copyShareableLink() {
    const base = window.location.origin + window.location.pathname;
    const url = `${base}${window.location.hash}`;
    navigator.clipboard.writeText(url).then(() => {
      showToast = true;
      setTimeout(() => {
        showToast = false;
      }, 2000);
    }).catch(err => {
      consoleMsg = { text: 'LINK COPY EXCEPTION: ' + err, isError: true };
    });
  }

  function copyEmbedLink() {
    const base = window.location.origin + window.location.pathname;
    const embedUrl = `${base}?embed=true${window.location.hash}`;
    navigator.clipboard.writeText(embedUrl).then(() => {
      showToast = true;
      setTimeout(() => {
        showToast = false;
      }, 2000);
    }).catch(err => {
      consoleMsg = { text: 'LINK COPY EXCEPTION: ' + err, isError: true };
    });
  }

  function exportSvg() {
    if (!svgElement) return;
    try {
      const svgClone = svgElement.cloneNode(true);
      const defs = svgClone.querySelector('defs');
      
      const styleEl = document.createElementNS('http://www.w3.org/2000/svg', 'style');
      styleEl.textContent = `
        @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;700&family=Share+Tech+Mono&display=swap');
        .svg-axis-line { stroke: #202020; stroke-width: 2px; }
        .svg-tick-line { stroke: #707070; stroke-width: 1.5px; }
        .svg-tick-text { font-family: 'JetBrains Mono', monospace; font-size: 10px; fill: #707070; text-anchor: middle; }
        .svg-group-title { font-family: 'Share Tech Mono', monospace; font-size: 12px; font-weight: bold; fill: #202020; letter-spacing: 1px; }
        .svg-grid-line-major { stroke: #e5e5e5; stroke-width: 1px; }
        .svg-leader-line { stroke: #607d8b; stroke-width: 1.5px; fill: none; stroke-linejoin: round; }
        .svg-balloon-rect { fill: #ffffff; stroke: #202020; stroke-width: 1.5px; }
        .balloon-content { width: 100%; height: 100%; padding: 0; display: flex; flex-direction: column; overflow: hidden; border-radius: 6px; font-family: 'JetBrains Mono', monospace; font-size: 10px; line-height: 1.3; color: #202020; }
        .balloon-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 1.5px solid #e5e5e5; padding: 5px 6px; }
        .balloon-date { font-weight: bold; color: #707070; }
        .balloon-title { font-weight: 700; color: #202020; text-overflow: ellipsis; white-space: nowrap; overflow: hidden; max-width: 110px; }
        .balloon-desc { flex: 1; padding: 6px; overflow: hidden; color: #404040; font-size: 9px; word-break: break-word; }
      `;
      defs.appendChild(styleEl);

      const contentGroupElement = svgClone.querySelector('#timeline-content');
      const bbox = contentGroupElement.getBBox();
      
      svgClone.setAttribute('width', (bbox.width + 120).toString());
      svgClone.setAttribute('height', (bbox.height + 160).toString());
      svgClone.setAttribute('viewBox', `${bbox.x - 60} ${bbox.y - 60} ${bbox.width + 120} ${bbox.height + 160}`);
      
      // Clean up transform scale for standard image render
      const exportGroup = svgClone.querySelector('#svg-zoom-group');
      exportGroup.removeAttribute('transform');

      const svgData = new XMLSerializer().serializeToString(svgClone);
      const svgBlob = new Blob([svgData], { type: 'image/svg+xml;charset=utf-8' });
      const svgUrl = URL.createObjectURL(svgBlob);
      
      const downloadLink = document.createElement('a');
      downloadLink.href = svgUrl;
      downloadLink.download = `spec-timeline-dwg-rev${Date.now().toString().substring(8)}.svg`;
      document.body.appendChild(downloadLink);
      downloadLink.click();
      document.body.removeChild(downloadLink);
      URL.revokeObjectURL(svgUrl);

      consoleMsg = { text: 'SUCCESS: Technical Vector SVG Drawing exported.', isError: false };
    } catch (err) {
      consoleMsg = { text: 'EXPORT SYSTEM EXCEPTION: ' + err, isError: true };
    }
  }
</script>

<div class="cad-app-container" class:embedded={isEmbedded}>
  <!-- CAD Frame Border Sheet -->
  <div class="cad-frame">
    
    <!-- Top Drawing Title Info Header -->
    {#if !isEmbedded}
      <header class="cad-header">
        <div class="header-section project-info">
          <span class="label">PROJECT:</span>
          <span class="value uppercase" id="header-project-name">SYSTEM LOG TIMELINE</span>
        </div>
        <div class="header-section doc-type">
          <span class="label">DOCUMENT TYPE:</span>
          <span class="value">ORTHOGRAPHIC TIMELINE GRAPH (SVELTE 5 + D3)</span>
        </div>
        <div class="header-section scale-info">
          <span class="label">SCALE:</span>
          <span class="value" id="header-scale">SCALE: {Math.round(zoomTransform.k * 100)}%</span>
        </div>
        <div class="header-section drawing-no">
          <span class="label">DWG NO:</span>
          <span class="value">TL-2026-D3-REV1</span>
        </div>
      </header>
    {/if}

    <!-- Workspace Area Split Panels -->
    <div class="cad-workspace">
      
      <!-- Left Controls Panel -->
      {#if !isEmbedded}
        <aside class="cad-control-panel">
          <div class="panel-section-title">1. DATA ENTRY SHEET</div>
        
        <div class="tab-controls">
          <button 
            class="tab-btn" 
            class:active={activeTab === 'text'} 
            onclick={() => activeTab = 'text'}>
            RAW TEXT DATA
          </button>
          <button 
            class="tab-btn" 
            class:active={activeTab === 'table'} 
            onclick={() => activeTab = 'table'}>
            BOM TABLE FORM
          </button>
        </div>

        <!-- Tab 1: Textarea Editor -->
        <div class="tab-content" class:active={activeTab === 'text'}>
          <div class="help-text">
            Format: <span class="highlight">EQUIPMENT | YYYY-MM-DD | TITLE | DESCRIPTION</span><br>
            Lines starting with <span class="highlight">//</span> or <span class="highlight">#</span> are ignored.
          </div>
          <textarea 
            id="raw-text-input" 
            spellcheck="false" 
            bind:value={rawText}
            oninput={parseRawText}
            placeholder="SYS-UNIT-01 | 2026-06-01 | EVENT_TITLE | Event description detail text.">
          </textarea>
        </div>

        <!-- Tab 2: BOM Form Grid Sheet -->
        <div class="tab-content" class:active={activeTab === 'table'}>
          <div class="table-actions">
            <button class="cad-btn-sm" onclick={addBomRow}>+ ADD ROW</button>
            <button class="cad-btn-sm warning-btn" onclick={clearAllEvents}>CLEAR ALL</button>
          </div>
          <div class="bom-table-container">
            <table id="bom-input-table">
              <thead>
                <tr>
                  <th>EQUIPMENT</th>
                  <th>DATE</th>
                  <th>TITLE</th>
                  <th>DESCRIPTION</th>
                  <th>DEL</th>
                </tr>
              </thead>
              <tbody>
                {#each events as event, idx}
                  <tr>
                    <td>
                      <input 
                        type="text" 
                        bind:value={event.equipment} 
                        oninput={onBomInput}
                      />
                    </td>
                    <td>
                      <input 
                        type="date" 
                        bind:value={event.date} 
                        oninput={onBomInput}
                      />
                    </td>
                    <td>
                      <input 
                        type="text" 
                        bind:value={event.title} 
                        oninput={onBomInput}
                      />
                    </td>
                    <td>
                      <input 
                        type="text" 
                        bind:value={event.desc} 
                        oninput={onBomInput}
                      />
                    </td>
                    <td>
                      <button 
                        class="bom-row-delete-btn" 
                        onclick={() => deleteBomRow(idx)}>
                        ×
                      </button>
                    </td>
                  </tr>
                {/each}
              </tbody>
            </table>
          </div>
        </div>

        <!-- Search & Filter Control Panel -->
        <div class="panel-section-title">2. SEARCH & FILTER</div>
        <div class="viewport-settings" style="display: flex; flex-direction: column; gap: 6px; margin-bottom: 8px;">
          <div style="position: relative; width: 100%;">
            <input 
              type="text" 
              placeholder="Search title, desc, date..." 
              bind:value={searchQuery}
              style="width: 100%; border: var(--line-normal); padding: 4px 24px 4px 6px; font-family: var(--font-mono); font-size: 11px; background-color: var(--color-pure-white); outline: none;"
            />
            {#if searchQuery}
              <button 
                onclick={() => searchQuery = ''}
                style="position: absolute; right: 6px; top: 50%; transform: translateY(-50%); background: none; border: none; font-size: 14px; cursor: pointer; color: var(--color-dim-gray); padding: 0; line-height: 1;"
                title="Clear search">
                ×
              </button>
            {/if}
          </div>
          {#if searchQuery}
            <div style="font-size: 9px; color: var(--color-dim-gray); font-style: italic;">
              Found {processedTimelineData.flatMap(r => r.events).filter(e => isHighlighted(e)).length} matching event{processedTimelineData.flatMap(r => r.events).filter(e => isHighlighted(e)).length !== 1 ? 's' : ''}.
            </div>
          {/if}
          
          {#if hiddenEventTitles.length > 0}
            <div style="display: flex; align-items: center; justify-content: space-between; margin-top: 4px; border-top: 1.5px dashed var(--color-light-gray); padding-top: 6px;">
              <span style="font-size: 10px; font-weight: bold; color: #c62828; font-family: var(--font-tech);">
                HIDDEN BALLOONS: {hiddenEventTitles.length}
              </span>
              <button 
                class="cad-btn-sm warning-btn" 
                style="font-size: 8px; padding: 2px 6px; height: auto;"
                onclick={showAllHiddenBalloons}>
                SHOW ALL
              </button>
            </div>
          {/if}
        </div>

        <!-- Timeline Visibility Toggles -->
        <div class="panel-section-title">3. TIMELINE VISIBILITY</div>
        <div class="viewport-settings" style="display: block; max-height: 120px; overflow-y: auto; margin-bottom: 8px;">
          {#each availableEquipments as eqName}
            <div class="settings-group" style="justify-content: flex-start; gap: 8px; margin-bottom: 4px;">
              <input 
                type="checkbox" 
                id="vis-{eqName}" 
                checked={!hiddenEquipments.includes(eqName)} 
                onchange={() => toggleEquipmentVisibility(eqName)}
                style="width: 14px; height: 14px; cursor: pointer; margin: 0; accent-color: var(--color-near-black);"
              />
              <label for="vis-{eqName}" style="cursor: pointer; font-size: 11px; font-family: var(--font-tech); font-weight: bold; color: var(--color-near-black);">{eqName}</label>
            </div>
          {/each}
          {#if availableEquipments.length === 0}
            <div style="font-size: 10px; color: var(--color-dim-gray); font-style: italic;">No equipment found.</div>
          {/if}
        </div>

        <!-- Config Control Panel -->
        <div class="panel-section-title">4. VIEWPORT CONFIGURATION</div>
        <div class="viewport-settings">
          <div class="settings-group">
            <label for="setting-balloon-width">BALLOON WIDTH (px):</label>
            <input type="number" id="setting-balloon-width" bind:value={balloonWidth} min="120" max="300" step="10">
          </div>
          <div class="settings-group">
            <label for="setting-balloon-height">BALLOON HEIGHT (px):</label>
            <input type="number" id="setting-balloon-height" bind:value={balloonHeight} min="50" max="150" step="5">
          </div>
          <div class="settings-group">
            <label for="setting-collision-gap">MIN GAP DISTANCE (px):</label>
            <input type="number" id="setting-collision-gap" bind:value={collisionGap} min="5" max="50" step="5">
          </div>
          <div class="settings-group">
            <label for="setting-leader-style">LEADER LINE STYLE:</label>
            <select id="setting-leader-style" bind:value={leaderStyle}>
              <option value="orthogonal">ORTHOGONAL 90°</option>
              <option value="diagonal">DIAGONAL (DIRECT)</option>
            </select>
          </div>
        </div>

        <!-- System Console Output -->
        <div class="feedback-console">
          <span class="label">SYSTEM CONSOLE:</span>
          <div id="console-output" class={consoleMsg.isError ? 'console-error' : 'console-green'}>
            {consoleMsg.text}
          </div>
        </div>
      </aside>
      {/if}

      <!-- Right CAD Graphic Viewport -->
      <main class="cad-viewport">
        <div class="viewport-header">
          <span class="viewport-title">GRAPHICS VIEWPORT</span>
          <div class="viewport-controls" style="display: flex; gap: 6px; align-items: center; position: relative;">
            <!-- Search input in header (Visible even in embedded mode) -->
            <div class="header-search-container" style="position: relative;">
              <input 
                type="text" 
                placeholder="Search balloons..." 
                bind:value={searchQuery}
                style="width: 140px; height: 20px; border: 1px solid #505050; padding: 2px 18px 2px 6px; font-family: var(--font-mono); font-size: 10px; background-color: #2e2e2e; color: #ffffff; outline: none; border-radius: 2px;"
              />
              {#if searchQuery}
                <button 
                  onclick={() => searchQuery = ''}
                  style="position: absolute; right: 4px; top: 50%; transform: translateY(-50%); background: none; border: none; font-size: 11px; cursor: pointer; color: #b0b0b0; padding: 0; line-height: 1;"
                  title="Clear search">
                  ×
                </button>
              {/if}
            </div>

            <!-- Timeline Visibility Dropdown popover button -->
            <div style="position: relative; display: inline-block;">
              <button 
                class="viewport-btn" 
                style="width: auto; padding: 0 8px; font-size: 9px; letter-spacing: 0.5px; display: flex; align-items: center; gap: 4px;"
                onclick={() => showVisibilityPopover = !showVisibilityPopover}
                title="Toggle Timelines">
                TIMELINES ▾
              </button>
              
              {#if showVisibilityPopover}
                <div class="floating-popover">
                  <div class="popover-title">TIMELINE VISIBILITY</div>
                  <div class="popover-content">
                    {#each availableEquipments as eqName}
                      <!-- svelte-ignore a11y_label_has_associated_control -->
                      <label class="popover-item">
                        <input 
                          type="checkbox" 
                          checked={!hiddenEquipments.includes(eqName)} 
                          onchange={() => toggleEquipmentVisibility(eqName)}
                        />
                        <span>{eqName}</span>
                      </label>
                    {/each}
                    {#if availableEquipments.length === 0}
                      <div class="popover-empty">No timelines.</div>
                    {/if}
                  </div>
                </div>
              {/if}
            </div>

            {#if hiddenEventTitles.length > 0}
              <button 
                class="viewport-btn warning-btn" 
                style="width: auto; padding: 0 8px; font-size: 9px; letter-spacing: 0.5px; font-family: var(--font-tech); font-weight: bold; cursor: pointer;"
                onclick={showAllHiddenBalloons}
                title="Reset Hidden Balloons">
                SHOW ALL ({hiddenEventTitles.length})
              </button>
            {/if}

            <span style="color: #505050; margin: 0 2px;">|</span>

            <button class="viewport-btn" onclick={zoomIn} title="Zoom In">+</button>
            <button class="viewport-btn" onclick={zoomOut} title="Zoom Out">-</button>
            <button class="viewport-btn" onclick={zoomReset} title="Reset Scale">1:1</button>
            <button class="viewport-btn" id="btn-zoom-reset" onclick={zoomToFit} title="Fit Content">FIT</button>
          </div>
        </div>

        <div id="drawing-area" class="drawing-area">
          <svg 
            bind:this={svgElement}
            id="timeline-svg" 
            xmlns="http://www.w3.org/2000/svg">
            
            <defs>
              <marker id="arrow" viewBox="0 0 10 10" refX="6" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
                <path d="M 0 1.5 L 10 5 L 0 8.5 z" fill="#505050" />
              </marker>
              <marker id="dot" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6">
                <circle cx="5" cy="5" r="3" fill="#202020" />
              </marker>
              <!-- Moving grid pattern definitions -->
              <pattern id="gridPattern" width="100" height="100" patternUnits="userSpaceOnUse">
                <!-- Minor Grid Lines (20px) -->
                <path d="M 20 0 L 20 100 M 40 0 L 40 100 M 60 0 L 60 100 M 80 0 L 80 100 M 0 20 L 100 20 M 0 40 L 100 40 M 0 60 L 100 60 M 0 80 L 100 80" 
                      stroke="#707070" stroke-width="0.5" opacity="0.04" />
                <!-- Major Grid Lines (100px) -->
                <path d="M 100 0 L 100 100 M 0 100 L 100 100" 
                      stroke="#707070" stroke-width="0.8" opacity="0.08" />
              </pattern>
            </defs>

            <!-- Zoomable Group container -->
            <g id="svg-zoom-group" transform="translate({zoomTransform.x}, {zoomTransform.y}) scale({zoomTransform.k})">
              <!-- SVG absolute moving grids behind elements -->
              <rect x="-20000" y="-20000" width="40000" height="40000" fill="url(#gridPattern)" />
              <g id="timeline-content">
                
                <!-- Loop over parallel timeline rows -->
                {#each processedTimelineData as row, rowIdx}
                  
                  <!-- Separator border line between equipment channels -->
                  {#if rowIdx > 0}
                    <line 
                      x1="10" 
                      y1={row.Y_axis - 150} 
                      x2={row.separatorEndX} 
                      y2={row.Y_axis - 150} 
                      class="svg-grid-line-major"
                      stroke-dasharray="6, 6" 
                    />
                  {/if}

                  <!-- Equipment Label Block tag -->
                  <g class="equipment-label-group">
                    <rect 
                      x="10" 
                      y={row.Y_axis - 20} 
                      width="150" 
                      height="40" 
                      rx="2"
                      ry="2"
                      fill={row.eqTheme.bg} 
                      stroke={row.eqTheme.stroke} 
                      stroke-width="1.5" 
                    />
                    <text 
                      x="20" 
                      y={row.Y_axis + 5} 
                      class="svg-group-title"
                      fill={row.eqTheme.text}>
                      {row.eqName.toUpperCase()}
                    </text>
                  </g>

                  <!-- Main axis line -->
                  <line 
                    x1={row.startX} 
                    y1={row.Y_axis} 
                    x2={row.endX} 
                    y2={row.Y_axis} 
                    class="svg-axis-line" 
                  />
                  
                  <!-- Left Terminal Boundary Cap -->
                  <line 
                    x1={row.startX} 
                    y1={row.Y_axis - 8} 
                    x2={row.startX} 
                    y2={row.Y_axis + 8} 
                    stroke="#202020" 
                    stroke-width="2" 
                  />

                  <!-- Right Terminal Boundary Cap -->
                  <line 
                    x1={row.endX} 
                    y1={row.Y_axis - 8} 
                    x2={row.endX} 
                    y2={row.Y_axis + 8} 
                    stroke="#202020" 
                    stroke-width="2" 
                  />

                  <!-- Render static dates ticks indicators on axis -->
                  {#each timelineTicks as tick}
                    <line 
                      x1={tick.x} 
                      y1={row.Y_axis - 4} 
                      x2={tick.x} 
                      y2={row.Y_axis + 4} 
                      stroke="var(--color-medium-gray)"
                      stroke-width="1"
                    />
                    <text 
                      x={tick.x} 
                      y={row.Y_axis - 8} 
                      font-size="8px"
                      fill="var(--color-dim-gray)"
                      text-anchor="middle"
                      font-family="var(--font-mono)">
                      {tick.dateStr}
                    </text>
                  {/each}

                  <!-- Render Events elements -->
                  <!-- Layer 1: Draw tick lines and leaders first (drawn underneath) -->
                  {#each row.events as event}
                    <!-- Tick Line on Timeline Axis -->
                    <line 
                      x1={event.xOrig} 
                      y1={row.Y_axis - 5} 
                      x2={event.xOrig} 
                      y2={row.Y_axis + 5} 
                      class="svg-tick-line" 
                      opacity={hoveredEventId ? (isHovered(event) ? 1 : 0.15) : (searchQuery.trim() && !isHighlighted(event) ? 0.3 : 1)}
                      style="transition: opacity 150ms;"
                    />

                    <!-- Connection Leader Line -->
                    <path 
                      class="svg-leader-line"
                      d={event.pathData}
                      stroke={isHovered(event) ? 'var(--color-const-blue)' : (isHighlighted(event) ? '#e65100' : (event.theme ? event.theme.stroke : 'var(--color-const-blue)'))}
                      stroke-width={isHovered(event) ? '2.5px' : (isHighlighted(event) ? '2.5px' : '1.5px')}
                      opacity={hoveredEventId ? (isHovered(event) ? 1 : 0.15) : (searchQuery.trim() && !isHighlighted(event) ? 0.3 : 1)}
                      style="transition: opacity 150ms;"
                    />

                    <!-- Axis Contact Point DOT (White-bordered thematic circle) -->
                    <circle
                      cx={event.xOrig}
                      cy={row.Y_axis}
                      r={isHovered(event) || isHighlighted(event) ? "6" : "4"}
                      fill={isHovered(event) ? 'var(--color-const-blue)' : (isHighlighted(event) ? '#e65100' : (event.theme ? event.theme.stroke : 'var(--color-const-blue)'))}
                      stroke="#ffffff"
                      stroke-width="1.5"
                      opacity={hoveredEventId ? (isHovered(event) ? 1 : 0.15) : (searchQuery.trim() && !isHighlighted(event) ? 0.3 : 1)}
                      style="transition: opacity 150ms, r 150ms;"
                    />
                  {/each}

                {/each}

                <!-- Layer 2: Draw info balloons on top (covering leader lines underneath) -->
                <!-- Loop over all events in visible rows, rendering non-hovered ones first -->
                {#each processedTimelineData as row}
                  {#each row.events as event}
                    {#if !isHovered(event)}
                      <!-- svelte-ignore a11y_no_static_element_interactions -->
                      <g 
                        class="svg-balloon-group" 
                        style="cursor: pointer; opacity: {hoveredEventId ? 0.2 : (searchQuery.trim() && !isHighlighted(event) ? 0.3 : 1)}; transition: opacity 150ms;"
                        role="presentation"
                        onclick={() => {
                          if (selectedEventId === event.id) {
                            selectedEventId = null;
                          } else {
                            selectedEventId = event.id;
                          }
                        }}
                        onmouseenter={() => hoveredEventId = event.id}
                        onmouseleave={() => hoveredEventId = null}>
                        
                        <!-- Rect shape: rx/ry 6px added for soft round corners, body fill is clean white, border colored by theme -->
                        <rect 
                          x={event.xBalloon - balloonWidth / 2} 
                          y={event.yBalloon} 
                          width={balloonWidth} 
                          height={event.H} 
                          rx="6"
                          ry="6"
                          fill="#ffffff"
                          stroke={selectedEventId === event.id ? 'var(--color-const-blue)' : (isHighlighted(event) ? '#e65100' : (event.theme ? event.theme.stroke : 'var(--color-near-black)'))}
                          stroke-width={selectedEventId === event.id ? '2.5' : (isHighlighted(event) ? '3' : '1.5')}
                          class="svg-balloon-rect"
                          class:active={selectedEventId === event.id}
                        />

                        <!-- HTML embedded inside SVG -->
                        <foreignObject 
                          x={event.xBalloon - balloonWidth / 2} 
                          y={event.yBalloon} 
                          width={balloonWidth} 
                          height={event.H}>
                          <div class="balloon-content">
                            <div class="balloon-header" style="background-color: {event.theme ? event.theme.bg : '#F5F5F5'}; border-bottom-color: {event.theme ? event.theme.stroke : 'var(--color-light-gray)'}; border-bottom-width: {selectedEventId === event.id ? '1.5px' : '0px'}">
                              <span class="balloon-title" style="color: {event.theme ? event.theme.stroke : 'var(--color-near-black)'}" title={event.title}>{event.title}</span>
                              <span class="balloon-date" style="color: {event.theme ? event.theme.stroke : 'var(--color-dim-gray)'}">{event.date}</span>
                            </div>
                            {#if selectedEventId === event.id}
                              <div class="balloon-desc" title={event.desc}>{event.desc}</div>
                              <div class="balloon-actions" style="display: flex; justify-content: space-between; padding: 4px 6px; border-top: 1px dashed var(--color-light-gray); background: #fcfcfc;">
                                <button 
                                  class="cad-btn-xs" 
                                  style="font-size: 8px; padding: 2px 4px; background: #ffffff; border: 1.5px solid var(--color-dim-gray); cursor: pointer; font-family: var(--font-tech); font-weight: bold; color: var(--color-near-black);"
                                  onclick={(e) => { e.stopPropagation(); hideSameBalloons(event.title); }}>
                                  HIDE SAME
                                </button>
                                <button 
                                  class="cad-btn-xs" 
                                  style="font-size: 8px; padding: 2px 4px; background: #ffffff; border: 1.5px solid var(--color-dim-gray); cursor: pointer; font-family: var(--font-tech); font-weight: bold; color: var(--color-near-black);"
                                  onclick={(e) => { e.stopPropagation(); selectedEventId = null; }}>
                                  CLOSE
                                </button>
                              </div>
                            {/if}
                          </div>
                        </foreignObject>
                      </g>
                    {/if}
                  {/each}
                {/each}

                <!-- Draw the hovered identical balloons last so they are always on top of all other elements -->
                {#each processedTimelineData as row}
                  {#each row.events as event}
                    {#if isHovered(event)}
                      <!-- svelte-ignore a11y_no_static_element_interactions -->
                      <g 
                        class="svg-balloon-group hovered" 
                        style="cursor: pointer; opacity: 1; transition: opacity 150ms;"
                        role="presentation"
                        onclick={() => {
                          if (selectedEventId === event.id) {
                            selectedEventId = null;
                          } else {
                            selectedEventId = event.id;
                          }
                        }}
                        onmouseenter={() => hoveredEventId = event.id}
                        onmouseleave={() => hoveredEventId = null}>
                        
                        <rect 
                          x={event.xBalloon - balloonWidth / 2} 
                          y={event.yBalloon} 
                          width={balloonWidth} 
                          height={event.H} 
                          rx="6"
                          ry="6"
                          fill="#ffffff"
                          stroke={selectedEventId === event.id ? 'var(--color-const-blue)' : (isHighlighted(event) ? '#e65100' : 'var(--color-const-blue)')}
                          stroke-width={selectedEventId === event.id ? '2.5' : (isHighlighted(event) ? '3' : '2.5')}
                          class="svg-balloon-rect active"
                        />

                        <foreignObject 
                          x={event.xBalloon - balloonWidth / 2} 
                          y={event.yBalloon} 
                          width={balloonWidth} 
                          height={event.H}>
                          <div class="balloon-content">
                            <div class="balloon-header" style="background-color: {event.theme ? event.theme.bg : '#F5F5F5'}; border-bottom-color: {event.theme ? event.theme.stroke : 'var(--color-light-gray)'}; border-bottom-width: {selectedEventId === event.id ? '1.5px' : '0px'}">
                              <span class="balloon-title" style="color: {event.theme ? event.theme.stroke : 'var(--color-near-black)'}" title={event.title}>{event.title}</span>
                              <span class="balloon-date" style="color: {event.theme ? event.theme.stroke : 'var(--color-dim-gray)'}">{event.date}</span>
                            </div>
                            {#if selectedEventId === event.id}
                              <div class="balloon-desc" title={event.desc}>{event.desc}</div>
                              <div class="balloon-actions" style="display: flex; justify-content: space-between; padding: 4px 6px; border-top: 1px dashed var(--color-light-gray); background: #fcfcfc;">
                                <button 
                                  class="cad-btn-xs" 
                                  style="font-size: 8px; padding: 2px 4px; background: #ffffff; border: 1.5px solid var(--color-dim-gray); cursor: pointer; font-family: var(--font-tech); font-weight: bold; color: var(--color-near-black);"
                                  onclick={(e) => { e.stopPropagation(); hideSameBalloons(event.title); }}>
                                  HIDE SAME
                                </button>
                                <button 
                                  class="cad-btn-xs" 
                                  style="font-size: 8px; padding: 2px 4px; background: #ffffff; border: 1.5px solid var(--color-dim-gray); cursor: pointer; font-family: var(--font-tech); font-weight: bold; color: var(--color-near-black);"
                                  onclick={(e) => { e.stopPropagation(); selectedEventId = null; }}>
                                  CLOSE
                                </button>
                              </div>
                            {/if}
                          </div>
                        </foreignObject>
                      </g>
                    {/if}
                  {/each}
                {/each}

              </g>
            </g>
          </svg>
        </div>
      </main>

    </div>

    <!-- Drawing Info Footer (Title Block) -->
    {#if !isEmbedded}
      <footer class="cad-footer">
        <div class="footer-status">
          <span class="blink">●</span> STATUS: NOMINAL (ONLINE)
        </div>

        <!-- Title Block (CAD Stamp) -->
        <div class="title-block">
          <div class="title-block-row">
            <div class="title-block-cell cell-2">
              <div class="tb-label">DRAWING TITLE</div>
              <div class="tb-val bold uppercase">TIMELINE ANALYSIS SCHEMATIC</div>
            </div>
            <div class="title-block-cell">
              <div class="tb-label">REVISION</div>
              <div class="tb-val font-large text-center">REV 1.0</div>
            </div>
          </div>
          <div class="title-block-row">
            <div class="title-block-cell">
              <div class="tb-label">CHECKED BY</div>
              <div class="tb-val uppercase">ANTIGRAVITY</div>
            </div>
            <div class="title-block-cell">
              <div class="tb-label">DATE</div>
              <div class="tb-val">{currentDate}</div>
            </div>
            <div class="title-block-cell cell-buttons">
              <button class="tb-action-btn" onclick={copyShareableLink}>COPY SHARE LINK</button>
              <button class="tb-action-btn" onclick={copyEmbedLink}>COPY EMBED LINK</button>
              <button class="tb-action-btn" onclick={exportSvg}>EXPORT SVG</button>
            </div>
          </div>
        </div>
      </footer>
    {/if}

  </div>
</div>

<!-- Copy toast alert notice -->
<div class="toast" class:show={showToast}>
  LINK COPIED TO CLIPBOARD
</div>
