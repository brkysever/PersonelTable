<template>
  <div class="mainDiv">
    <input
      class="customerSearch"
      type="search"
      v-model="search"
      placeholder="Search Customer Name"
    />
    <table>
      <thead>
        <tr>
          <th
            v-for="col in columns"
            :key="col.key"
            @click="col.sortable && sortTable(col.key)"
            :class="{ sortable: col.sortable }"
          >
            {{ col.label }}

            <div
              class="arrow"
              v-if="col.key == sortColumn"
              :class="ascending ? 'arrow_up' : 'arrow_down'"
            ></div>
          </th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="(row, index) of filteredRows" :key="row.id">
          <td v-for="col of columns" :key="col.key">
            <PhoneEditable
              v-if="col.key === 'phone'"
              :phone="row[col.key]"
              @phone-changed="changePhone($event, index)"
            />
            <p v-else>{{ row[col.key] }}</p>
            <a
              v-if="col.key === 'delete'"
              :="row[col.key]"
              @click="deleteRows(index)"
            >
              <img
                class="trashBox"
                src="https://image.flaticon.com/icons/png/512/1214/1214428.png"
              />
            </a>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import PhoneEditable from "./PhoneEditable.vue";
import faker from "faker";

function createCustomer() {
  return {
    id: faker.datatype.number(),
    name: faker.name.findName(),
    phone: faker.phone.phoneNumber(),
    profession: faker.name.jobTitle(),
  };
}
export default {
  name: "CustomerTable",
  components: {
    PhoneEditable,
  },
  data() {
    return {
      ascending: false,
      sortColumn: "",

      columns: [
        {
          key: "id",
          label: "id",
          sortable: true,
        },
        {
          key: "name",
          label: "name",
          sortable: true,
        },
        {
          key: "phone",
          label: "phone",
          sortable: false,
        },
        {
          key: "profession",
          label: "profession",
          sortable: true,
        },
        {
          key: "delete",
          label: "",
          sortable: false,
        },
      ],

      rows: [
        createCustomer(),
        createCustomer(),
        createCustomer(),
        createCustomer(),
        createCustomer(),
        createCustomer(),
        createCustomer(),
        createCustomer(),
        createCustomer(),
        createCustomer(),
      ],
      search: "",
    };
  },

  methods: {
    changePhone(newPhone, index) {
      this.rows[index].phone = newPhone;
    },
    sortTable(col) {
      if (this.sortColumn === col) {
        this.ascending = !this.ascending;
      } else {
        this.ascending = true;
        this.sortColumn = col;
      }

      this.rows.sort((a, b) => {
        if (a[col] > b[col]) {
          return this.ascending ? 1 : -1;
        } else if (a[col] < b[col]) {
          return this.ascending ? -1 : 1;
        }
        return 0;
      });
    },
    deleteRows(index) {
      this.rows.splice(index, 1);
    },
  },
  computed: {
    filteredRows: function () {
      return this.rows.filter((rows) => {
        return rows.name.includes(this.search);
      });
    },
  },
};
</script>

<style>
body {
  background-color: #f1f2f7;
}

table {
  font-family: "Open Sans", sans-serif;
  width: 750px;
  border-collapse: collapse;
  border: 3px solid #3a3a3a;
  margin: 10px 10px 0 10px;
}

table th {
  text-transform: uppercase;
  text-align: left;
  background: #3a3a3a;
  color: #fff;
  padding: 8px;
  min-width: 30px;
}
table th.sortable:hover {
  cursor: pointer;
  background: #7e7e7e;
}
table td {
  text-align: left;
  padding: 8px;
  border-right: 2px solid #3a3a3a;
}
table td:last-child {
  border-right: none;
}
table tbody tr:nth-child(2n) td {
  background: #88888844;
}

.arrow_down {
  background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB8AAAAaCAYAAABPY4eKAAAAAXNSR0IArs4c6QAAAvlJREFUSA29Vk1PGlEUHQaiiewslpUJiyYs2yb9AyRuJGm7c0VJoFXSX9A0sSZN04ULF12YEBQDhMCuSZOm1FhTiLY2Rky0QPlQBLRUsICoIN/0PCsGyox26NC3eTNn3r3n3TvnvvsE1PkwGo3yUqkkEQqFgw2Mz7lWqwng7ztN06mxsTEv8U0Aam5u7r5EInkplUol/f391wAJCc7nEAgE9Uwmkzo4OPiJMa1Wq6cFs7Ozt0H6RqlUDmJXfPIx+qrX69Ti4mIyHA5r6Wq1egND+j+IyW6QAUoul18XiUTDNHaSyGazKcZtdgk8wqhUKh9o/OMvsVgsfHJy0iWqVrcQNRUMBnd6enqc9MjISAmRP3e73T9al3XnbWNjIw2+KY1Gc3imsNHR0YV4PP5+d3e32h3K316TySQFoX2WyWR2glzIO5fLTSD6IElLNwbqnFpbWyO/96lCoai0cZjN5kfYQAYi5H34fL6cxWIZbya9iJyAhULBHAqFVlMpfsV/fHxMeb3er+Vy+VUzeduzwWC45XA4dlD/vEXvdDrj8DvURsYEWK3WF4FA4JQP9mg0WrHZbEYmnpa0NxYgPVObm5teiLABdTQT8a6vrwdRWhOcHMzMzCiXlpb2/yV6qDttMpkeshEzRk4Wo/bfoe4X9vb2amzGl+HoXNT29vZqsVi0sK1jJScG+Xx+HGkL4Tew2TPi5zUdQQt9otPpuBk3e0TaHmMDh1zS7/f780S0zX6Yni+NnBj09fUZUfvudDrNZN+GkQbl8Xi8RLRtHzsB9Hr9nfn5+SjSeWUCXC7XPq5kw53wsNogjZNohYXL2EljstvtrAL70/mVaW8Y4OidRO1/gwgbUMvcqGmcDc9aPvD1gnTeQ+0nmaInokRj0nHh+uvIiVOtVvt2a2vLv7Ky0tL3cRTXIcpPAwMDpq6R4/JXE4vFQ5FI5CN+QTaRSFCYc8vLy1l0rge4ARe5kJ/d27kYkLXoy2Jo4C7K8CZOsEBvb+9rlUp1xNXPL7v3IDwxvPD6AAAAAElFTkSuQmCC");
}
.arrow_up {
  background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAaCAYAAACgoey0AAAAAXNSR0IArs4c6QAAAwpJREFUSA21Vt1PUmEYP4dvkQ8JFMwtBRocWAkDbiqXrUWXzU1rrTt0bdVqXbb1tbW16C9IBUSmm27cODdneoXjputa6069qwuW6IIBIdLvdaF4OAcOiGeDc87zPs/vd57P96WpFq7p6enbGo1mjKZpeTabjU1MTCRagGnOZHFxcXxtbe1XKpUq7+zslJeXl//Mz8+Hy+Uy3RxSE9qTk5M3otFooVQqgef4Wl9f343FYoEmoISrxuNxFX5f9vb2jhn/PxUKhfLS0tIPfFifUESRUMV8Pv/M6XReRm5rTGQyGeXxeGxYe1ezeBpBOBx2rKysbO7v79d4Wy3Y2Nj4GQqFbgnhaugxwiuGJx99Pp9FLBbXxYTXvTqd7v3MzIy6riIWGxJnMpl7AwMD14xGYyMsSq1WUyQdUqn0eSPlusQIsbGrq+vl4OCgvhFQZd1utyv1en0gEolcqsi47nWJlUrlG5fLZVcoFFy2nDKSDpIWlUoVTCQSEk4lCHmJMZ2GTCbTiMVikfIZ88l7enoos9l8dXt7+z6fDicxSJUokqDX6xXcl2wCROoc0vQCWL3sNfLOSdzR0fHY4XC4tVotl40gmVwup9xuN4OQv+UyqCFGH9rg7SOGYVRcBs3IEG4J0nVnamrqOtvuBDGGgQg9+wHFcVEi4a0LNkbdd6TrPKo8ODc311mteIIYjT/a398/jK+s1jnVM0kXoufCFvq0GuiIGEVgQIhfoygM1QrteEa9dAL7ITiYCt4RMabOK5AyKKzKWtvupLcRciu8D5J0EuDDPyT/Snd39yh6VtY2NhYQSR9G79Ds7OxdskRjEyAufvb7/cPoO5Z6e1+xtVKrq6vfcFzyi/A3ZrPZ3GdNSlwgo5ekE4X2RIQGf2C1WlufFE0GBeGWYQ8YERWLxQtnUVB830MKLZfL9RHir8lkssCn2G751tZWEWe03zTKm15YWPiEiXXTYDB0Ig/t7yd8PRws4EicwWHxO4jHD8/C5HiTTqd1BwcHFozKU89origB+y/kmzgYpgOBQP4fGmUiZmJ+WNgAAAAASUVORK5CYII=");
}
.arrow {
  float: right;
  width: 12px;
  height: 15px;
  background-repeat: no-repeat;
  background-size: contain;
  background-position-y: bottom;
}
.trashBox {
  width: 20px;
  height: 23px;
  padding-left: 6px;
  padding-bottom: 7px;
  cursor: pointer;
}
.customerSearch {
  border: 3px solid #44475c;
  margin: 10px 10px 0 10px;
}
.mainDiv {
  width: 50%;
}

@media only screen and (max-width: 768px) {
  table {
    width: 600px;
  }
}
@media only screen and (max-width: 615px) {
  table {
    width: 450px;
  }
}
@media only screen and (max-width: 500px) {
  table {
    width: 400px;
  }
}
</style>
