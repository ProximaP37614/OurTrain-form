<script>
  export let data;
  let trips = data.trips;
  let stations = data.stations;
  let editingTripId = null;
  let editingStationId = null;
  let showAddTripForm = false;  // Toggle visibility of the Add Trip form
  let showAddStationForm = false

  let newTrip = {
    trip_id: '',
    start: '',
    end: '',
    from_datetime: '',
    arrivalTime: '',
    class: '',
    seats: '',
    staff_id: ''
  };

  let newStation = {
    station_id: '',
    station_name: '',
    station_address: '',
    time_use: '',
    station_status: ''
  };

  function editTrip(tripId) {
    editingTripId = tripId;
  }

  function editStation(stationId) {
    editingStationId = stationId;
    console.log(stations)
  }

  async function saveTrip(tripId) {
    let st_name = []
    for (let i = 0; i < stations.length; i++) {
      st_name.push(stations[i]["station_name"]);
    }
    const formData = new FormData();
    const trip = trips.find(t => t.trip_id === tripId);
    formData.append('tripId', tripId);
    formData.append('start', trip.start);
    formData.append('end', trip.end);
    formData.append('fromDatetime', trip.from_datetime);

    if ((st_name.includes(trip.start)) && (st_name.includes(trip.end))) {

      try {
          const response = await fetch('/tor/?/updateTrip', {
              method: 'POST',
              body: formData
          });

          const result = await response.json();

          // Log the result for debugging
          console.log('Result from server:', result);
          if (result.type == 'success') {
              alert('Trip updated successfully!');
          } else {
              alert('error.');
          }

      } catch (error) {
          console.error('Fetch error:', error);
          alert('An error occurred while updating the trip.');
      }

      editingTripId = null; // Exit edit mode
  }

  else {
    alert('กรุณากรอกสถานี ต้นทาง/ปลายทาง ให้ถูกต้อง');
  }
}

async function saveStation(stationId) {
    let st_name = []
    for (let i = 0; i < stations.length; i++) {
      st_name.push(stations[i]["station_name"]);
    }
    const formData = new FormData();
    const station = stations.find(s => s.station_id === stationId);
    formData.append('stationId', stationId);
    formData.append('name', station.station_name);
    formData.append('address', station.station_address);
    formData.append('time', station.time_use);
    formData.append('status', station.statio_status);

    if (st_name.includes(station.station_name)) {

      try {
          const response = await fetch('/tor/?/updateStation', {
              method: 'POST',
              body: formData
          });

          const result = await response.json();

          // Log the result for debugging
          console.log('Result from server:', result);
          if (result.type == 'success') {
              alert('Station updated successfully!');
          } else {
              alert('error.');
              //console.log(stations[0])
          }

      } catch (error) {
          console.error('Fetch error:', error);
          alert('An error occurred while updating the station.');
      }

      editingStationId = null; // Exit edit mode
  }

  else {
    alert('กรุณากรอกชื่อสถานีให้ถูกต้อง');
  }
}

  async function deleteTrip(tripId) {
    if (editingTripId === tripId) {
      alert("คุณกำลังแก้ไขเที่ยวโดยสารอยู่ กรุณาออกจากการแก้ไขเที่ยวโดยสาร ก่อนลบเที่ยวโดยสาร");
      return;
    }

    const confirmDelete = confirm("คุณแน่ใจหรือไม่ว่าต้องการลบเที่ยวโดยสารนี้?");
    if (!confirmDelete) {
      return;
    }

    try {
      const response = await fetch('/tor/?/deleteTrip', {
        method: 'POST',
        body: new URLSearchParams({ tripId })
      });

      const result = await response.json();

      if (result.type == 'success') {
        trips = trips.filter(trip => trip.trip_id !== tripId);
        alert("ลบเที่ยวโดยสารสำเร็จ");
      } else {
        alert("เกิดข้อผิดพลาดในการลบเที่ยวโดยสาร");
      }
    } catch (error) {
      console.error('Error deleting trip:', error);
      alert("เกิดข้อผิดพลาดในการลบเที่ยวโดยสาร");
    }
  }

  async function deleteStation(stationId) {
    if (editingStationId === stationId) {
      alert("คุณกำลังแก้ไขสถานีรถไฟอยู่ กรุณาออกจากการแก้ไขสถานีรถไฟ ก่อนลบสถานีรถไฟ");
      return;
    }

    const confirmDelete = confirm("คุณแน่ใจหรือไม่ว่าต้องการลบสถานีรถไฟนี้?");
    if (!confirmDelete) {
      return;
    }

    try {
      const response = await fetch('/tor/?/deleteStation', {
        method: 'POST',
        body: new URLSearchParams({ stationId })
      });

      const result = await response.json();

      if (result.type == 'success') {
        stations = stations.filter(station => station.station_id !== stationId);
        alert("ลบสถานีรถไฟสำเร็จ");
      } else {
        alert("เกิดข้อผิดพลาดในการลบสถานีรถไฟ");
      }
    } catch (error) {
      console.error('Error deleting station:', error);
      alert("เกิดข้อผิดพลาดในการลบสถานีรถไฟ");
    }
  }

  // Toggle visibility of the Add Trip form
  function toggleAddTripForm() {
    showAddTripForm = !showAddTripForm;
  }

  function toggleAddStationForm() {
    showAddStationForm = !showAddStationForm;
  }

  // Function to save a new trip
  async function addNewTrip() {
    const formData = new FormData();
    formData.append('tripId', newTrip.trip_id);
    formData.append('start', newTrip.start);
    formData.append('end', newTrip.end);
    formData.append('fromDatetime', newTrip.from_datetime);
    formData.append('arrivalTime', newTrip.arrivalTime);
    formData.append('class', newTrip.class);
    formData.append('seats', newTrip.seats);
    formData.append('staff_id', newTrip.staff_id);

    try {
      const response = await fetch('/tor/?/addTrip', {
        method: 'POST',
        body: formData
      });

      const result = await response.json();

      if (result.type == 'success') {
        trips = [...trips, newTrip];  // Add the new trip to the list
        alert('Trip added successfully!');
        showAddTripForm = false;  // Hide the form after successful addition
      } else {
        alert('Error adding trip.');
      }
    } catch (error) {
      console.error('Error adding trip:', error);
      alert('An error occurred while adding the trip.');
    }
  }

  async function addNewStation() {
    const formData = new FormData();
    formData.append('station_id', newStation.station_id);
    formData.append('station_name', newStation.station_name);
    formData.append('station_address', newStation.station_address);
    formData.append('time_use', newStation.time_use);
    formData.append('station_status', newStation.station_status);

    try {
      const response = await fetch('/tor/?/addStation', {
        method: 'POST',
        body: formData
      });

      const result = await response.json();

      if (result.type == 'success') {
        stations = [...stations, newStation];  // Add the new trip to the list
        alert('Station added successfully!');
        showAddStationForm = false;  // Hide the form after successful addition
      } else {
        alert('Error adding station.');
      }
    } catch (error) {
      console.error('Error adding station:', error);
      alert('An error occurred while adding the station.');
    }
  }
</script>

<!-- Main content -->
<div class="flex px-8 lg:px-12 justify-center min-h-screen bg-white">
  <div class="container mx-auto p-4">
    <h1 class="text-3xl font-bold mb-8 text-center text-[#102C57] underline">จัดการเที่ยวโดยสาร</h1>
    <h2 class="text-xl font-bold mb-4">เที่ยวโดยสารปัจจุบัน</h2>
    <section class="overflow-auto h-96">
      <table class="table-auto w-full text-left border-collapse whitespace-nowrap">
        <thead class="sticky top-0">
          <tr class="bg-gray-200">
            <th class="px-2 py-2 sm:px-4 sm:py-2">รหัสเที่ยวโดยสาร</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">ต้นทาง</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">ปลายทาง</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">วัน / เวลาที่ออก</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">เวลาที่ถึง</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">ชั้นโดยสาร</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">จำนวนที่นั่ง</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">พนักงานตรวจ</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">จัดการเที่ยวโดยสาร</th>
          </tr>
        </thead>
        <tbody>
          {#each trips as trip}
            <tr class="even:bg-gray-100">
              <td class="border px-2 py-2 sm:px-4 sm:py-2">{trip.trip_id}</td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">
                {#if editingTripId === trip.trip_id}
                  <input type="text" bind:value={trip.start} class="w-full border p-1" />
                {:else}
                  {trip.start}
                {/if}
              </td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">
                {#if editingTripId === trip.trip_id}
                  <input type="text" bind:value={trip.end} class="w-full border p-1" />
                {:else}
                  {trip.end}
                {/if}
              </td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">
                {#if editingTripId === trip.trip_id}
                  <input type="datetime-local" bind:value={trip.from_datetime} class="w-full border p-1" />
                {:else}
                  {trip.from_datetime}
                {/if}
              </td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">{trip.arrivalTime}</td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">{trip.class}</td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">{trip.seats}</td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">{trip.staff_id}</td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2 flex flex-col sm:flex-row">
                {#if editingTripId === trip.trip_id}
                  <button 
                    on:click={() => saveTrip(trip.trip_id)} 
                    class="bg-green-500 text-white w-full sm:w-32 px-2 py-2 sm:px-4 sm:py-2 rounded mb-2 sm:mb-0 sm:mr-2">
                    บันทึก
                  </button>
                {:else}
                  <button 
                    on:click={() => editTrip(trip.trip_id)} 
                    class="bg-[#102C57] text-white w-full sm:w-32 px-2 py-2 sm:px-4 sm:py-2 rounded mb-2 sm:mb-0 sm:mr-2">
                    แก้ไข
                  </button>
                {/if}
                <button 
                  on:click={() => deleteTrip(trip.trip_id)} 
                  class="bg-red-500 text-white w-full sm:w-32 px-2 py-2 sm:px-4 sm:py-2 rounded">
                  ลบเที่ยวโดยสาร
                </button>
              </td>
              
            </tr>
          {/each}
        </tbody>
      </table>
    </section>

    <!-- Add Trip Button -->
    <div class="flex justify-center items-center">
      <button on:click={toggleAddTripForm} class="bg-[#102C57] text-white w-full mt-10 sm:w-1/6 px-2 py-2 sm:px-4 sm:py-2 rounded">เพิ่มเที่ยวโดยสาร</button>
    </div>

    <!-- Add Trip Form -->
    {#if showAddTripForm}
      <div class="mt-8">
        <h3 class="text-lg font-bold mb-4">เพิ่มเที่ยวโดยสารใหม่</h3>
        <table class="table-auto w-full text-left border-collapse whitespace-nowrap">
          <thead>
            <tr class="bg-gray-200">
              <th class="px-2 py-2 sm:px-4 sm:py-2">รหัสเที่ยวโดยสาร</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">ต้นทาง</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">ปลายทาง</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">วัน / เวลาที่ออก</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">เวลาที่ถึง</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">ชั้นโดยสาร</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">จำนวนที่นั่ง</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">พนักงานตรวจ</th>
            </tr>
          </thead>
          <tbody>
            <tr class="even:bg-gray-100">
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newTrip.trip_id} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newTrip.start} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newTrip.end} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="datetime-local" bind:value={newTrip.from_datetime} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newTrip.arrivalTime} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newTrip.class} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="number" bind:value={newTrip.seats} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newTrip.staff_id} class="w-full border p-1" /></td>
            </tr>
          </tbody>
        </table>

        <div class="flex justify-end mt-4">
          <button on:click={addNewTrip} class="bg-green-500 text-white px-4 py-2 rounded">บันทึก</button>
        </div>
      </div>
    {/if}
  </div>
</div>

<!-- Main content -->
<div class="flex px-8 mb-48 lg:px-12 justify-center bg-white">
  <div class="container mx-auto px-4">
    <h2 class="text-xl font-bold mb-4">สถานีรถไฟ</h2>
    <section class="overflow-auto h-96">
      <table class="table-auto w-full text-left border-collapse whitespace-nowrap">
        <thead class="sticky top-0">
          <tr class="bg-gray-200">
            <th class="px-2 py-2 sm:px-4 sm:py-2">รหัสสถานี</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">ชื่อสถานี</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">ที่อยู่</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">ระยะเวลา</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">สถานะการใช้งาน</th>
            <th class="px-2 py-2 sm:px-4 sm:py-2">จัดการสถานีรถไฟ</th>
          </tr>
        </thead>
        <tbody>
          {#each stations as station}
            <tr class="even:bg-gray-100">
              <td class="border px-2 py-2 sm:px-4 sm:py-2">{station.station_id}</td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">
                {#if editingStationId === station.station_id}
                  <input type="text" bind:value={station.station_name} class="w-full border p-1" />
                {:else}
                  {station.station_name}
                {/if}
              </td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">
                {#if editingStationId === station.station_id}
                  <input type="text" bind:value={station.station_address} class="w-full border p-1" />
                {:else}
                  {station.station_address}
                {/if}
              </td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">
                {#if editingStationId === station.station_id}
                  <input type="datetime-local" bind:value={station.time_use} class="w-full border p-1" />
                {:else}
                  {station.time_use}
                {/if}
              </td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2">
                  {#if editingStationId === station.station_id}
                  <input type="text" bind:value={station.station_status} class="w-full border p-1" />
                {:else}
                  {station.station_status}
                {/if}
              </td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2 flex flex-col sm:flex-row justify-center items-center">
                {#if editingStationId === station.station_id}
                  <button 
                    on:click={() => saveStation(station.station_id)} 
                    class="bg-green-500 text-white w-full sm:w-32 px-2 py-2 sm:px-4 sm:py-2 rounded mb-2 sm:mb-0 sm:mr-2">
                    บันทึก
                  </button>
                {:else}
                  <button 
                    on:click={() => editStation(station.station_id)} 
                    class="bg-[#102C57] text-white w-full sm:w-32 px-2 py-2 sm:px-4 sm:py-2 rounded mb-2 sm:mb-0 sm:mr-2">
                    แก้ไข
                  </button>
                {/if}
                <button 
                  on:click={() => deleteStation(station.station_id)} 
                  class="bg-red-500 text-white w-full sm:w-32 px-2 py-2 sm:px-4 sm:py-2 rounded">
                  ลบสถานีรถไฟ
                </button>
              </td>
              
            </tr>
          {/each}
        </tbody>
      </table>
    </section>

    <!-- Add Trip Button -->
    <div class="flex justify-center items-center">
      <button on:click={toggleAddStationForm} class="bg-[#102C57] text-white w-full mt-10 sm:w-1/6 px-2 py-2 sm:px-4 sm:py-2 rounded">เพิ่มสถานีรถไฟ</button>
    </div>

    <!-- Add Trip Form -->
    {#if showAddStationForm}
      <div class="mt-8">
        <h3 class="text-lg font-bold mb-4">เพิ่มเที่ยวโดยสารใหม่</h3>
        <table class="table-auto w-full text-left border-collapse whitespace-nowrap">
          <thead>
            <tr class="bg-gray-200">
              <th class="px-2 py-2 sm:px-4 sm:py-2">รหัสสถานี</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">ชื่อสถานี</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">ที่อยู่</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">ระยะเวลา</th>
              <th class="px-2 py-2 sm:px-4 sm:py-2">สถานะการใช้งาน</th>
            </tr>
          </thead>
          <tbody>
            <tr class="even:bg-gray-100">
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newStation.station_id} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newStation.station_name} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newStation.station_address} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="text" bind:value={newStation.time_use} class="w-full border p-1" /></td>
              <td class="border px-2 py-2 sm:px-4 sm:py-2"><input type="number" bind:value={newStation.station_status} class="w-full border p-1" /></td>
            </tr>
          </tbody>
        </table>

        <div class="flex justify-end mt-4">
          <button on:click={addNewStation} class="bg-green-500 text-white px-4 py-2 rounded">บันทึก</button>
        </div>
      </div>
    {/if}
  </div>
</div>
