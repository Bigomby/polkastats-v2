<template>
  <div>
    <section>
      <b-container class="page-accounts main pt-4">
        <h1 class="text-center mb-4">Active Kusama accounts</h1>

        <!-- Info message -->
        <b-alert show dismissible variant="success" class="text-center mb-4">
          Showing <strong>{{ formatNumber(accounts.length) }} active Kusama accounts</strong>, updated every 5 minutes. Click on account to see full details.
        </b-alert>

        <!-- Filter -->
        <b-row>
          <b-col lg="12" class="mb-4">
            <b-form-input
              v-model="filter"
              type="search"
              id="filterInput"
              placeholder="Search account by address, index or identity"
            ></b-form-input>
          </b-col>
        </b-row>
        <!-- Mobile sorting -->
        <div class="row d-block d-sm-block d-md-block d-lg-none d-xl-none">
          <b-col lg="6" class="my-1">
            <b-form-group
              label="Sort"
              label-cols-sm="3"
              label-align-sm="right"
              label-size="sm"
              label-for="sortBySelect"
              class="mb-4"
            >
              <b-input-group size="sm">
                <b-form-select v-model="sortBy" id="sortBySelect" :options="sortOptions" class="w-75">
                  <template v-slot:first>
                    <option value="">-- none --</option>
                  </template>
                </b-form-select>
                <b-form-select v-model="sortDesc" size="sm" :disabled="!sortBy" class="w-25">
                  <option :value="false">Asc</option>
                  <option :value="true">Desc</option>
                </b-form-select>
              </b-input-group>
            </b-form-group>
          </b-col>
        </div>
        <!-- Table with sorting and pagination-->
        <div>
          <b-table
            stacked="md"
            id="accounts-table"
            head-variant="dark"
            :fields="fields"
            :items="accounts"
            :per-page="perPage"
            :current-page="currentPage"
            :sort-by.sync="sortBy"
            :sort-desc.sync="sortDesc"
            :filter="filter"
            :filterIncludedFields="filterOn"
            @filtered="onFiltered"
          >
            <template slot="rank" slot-scope="data">
              <p class="text-right mb-0">
                #{{ data.item.rank }}
              </p>
            </template>
            <template slot="accountId" slot-scope="data">
              <div class="d-block d-sm-block d-md-none d-lg-none d-xl-none text-center">
                <p class="mb-2">
                  rank #{{ data.item.rank }}
                </p>
                <Identicon :value="data.item.accountId" :size="40" :theme="'polkadot'" :key="data.item.accountId" />
                <nuxt-link :to="{name: 'account', query: { accountId: data.item.accountId } }" title="Account details">
                  <h4>{{ data.item.accountIndex }}</h4>
                </nuxt-link>
                <p class="mb-0" v-if="data.item.identity.display">
                  {{ data.item.identity.display }}
                </p>
                <table class="table table-striped mt-4">
                  <tbody>
                    <tr>
                      <td class="text-left"><strong>Free Balance</strong></td>
                      <td class="text-right">{{ formatAmount(data.item.freeBalance) }}</td>
                    </tr>
                    <tr>
                      <td class="text-left"><strong>Available Balance</strong></td>
                      <td class="text-right">{{ formatAmount(data.item.availableBalance) }}</td>
                    </tr>
                    <tr>
                      <td class="text-left"><strong>Locked Balance</strong></td>
                      <td class="text-right">{{ formatAmount(data.item.lockedBalance) }}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <div class="d-none d-sm-none d-md-block d-lg-block d-xl-block">
                <Identicon :value="data.item.accountId" :size="20" :theme="'polkadot'" :key="data.item.accountId" />
                <nuxt-link :to="{name: 'account', query: { accountId: data.item.accountId } }" title="Account details">
                  {{ data.item.accountIndex }}
                </nuxt-link>
              </div>
            </template>
            <template slot="identity" slot-scope="data">
              {{ data.item.identity.display }}
            </template>
            <template slot="freeBalance" slot-scope="data">
              <p class="text-right mb-0">
                {{ formatAmount(data.item.freeBalance) }}
              </p>
            </template>
            <template slot="lockedBalance" slot-scope="data">
              <p class="text-right mb-0">
                {{ formatAmount(data.item.lockedBalance) }}
              </p>
            </template>
            <template slot="availableBalance" slot-scope="data">
              <p class="text-right mb-0">
                {{ formatAmount(data.item.availableBalance) }}
              </p>
            </template>
          </b-table>
          <b-pagination
            v-model="currentPage"
            :total-rows="totalRows"
            :per-page="perPage"
            aria-controls="events-table"
          ></b-pagination>
        </div>
      </b-container>
    </section>
  </div>
</template>
<script>
import { mapMutations } from 'vuex';
import axios from 'axios';
import bootstrap from 'bootstrap';
import Identicon from '../components/identicon.vue';
import commonMixin from '../mixins/commonMixin.js';

export default {
  head () {
    return {
      title: 'PolkaStats - Polkadot Kusama active accounts',
      meta: [
        { hid: 'description', name: 'description', content: 'Polkadot Kusama active accounts' }
      ]
    }
  },
  mixins: [commonMixin],
  data: function() {
    return {
      perPage: 10,
      currentPage: 1,
      sortBy: `rank`,
      sortDesc: false,
      filter: null,
      filterOn: [],
      totalRows: 1,
      fields: [
        { key: 'rank', label: 'Rank', sortable: true, class: `d-none d-sm-none d-md-table-cell d-lg-table-cell d-xl-table-cell` },
        { key: 'accountId', label: 'Account', sortable: true },
        { key: 'identity', label: 'Identity', sortable: true, class: `d-none d-sm-none d-md-table-cell d-lg-table-cell d-xl-table-cell` },
        { key: 'freeBalance', label: 'Free balance', sortable: true, class: `d-none d-sm-none d-md-table-cell d-lg-table-cell d-xl-table-cell` },
        { key: 'lockedBalance', label: 'Locked balance', sortable: true, class: `d-none d-sm-none d-md-table-cell d-lg-table-cell d-xl-table-cell` },
        { key: 'availableBalance', label: 'Available balance', sortable: true, class: `d-none d-sm-none d-md-table-cell d-lg-table-cell d-xl-table-cell` }
      ],
      polling: null
    }
  },
  computed: {
    accounts() {
      return this.$store.state.accounts.list;
    },
    sortOptions() {
      // Create an options list from our fields
      return this.fields
        .filter(f => f.sortable)
        .map(f => {
          return { text: f.label, value: f.key }
        });
    }
  },
  created: function () {
    var vm = this;
    
    // Force update of account list if empty
    if (this.$store.state.accounts.list.length == 0) {
      vm.$store.dispatch('accounts/update');
    }
    this.totalRows = this.$store.state.accounts.list.length;

    // Update data every 5 minutes
    this.polling = setInterval(() => {
      vm.$store.dispatch('accounts/update');
      if (!this.filter) this.totalRows = this.$store.state.accounts.list.length;
    }, 300000);

  },
  beforeDestroy: function () {
    clearInterval(this.polling);
  },
  methods: {
    onFiltered(filteredItems) {
      // Trigger pagination to update the number of buttons/pages due to filtering
      this.totalRows = filteredItems.length
      this.currentPage = 1
    }
  },
  components: {
    Identicon
  }
}
</script>
<style>
#accounts-table th {
  text-align: center;
}
.page-accounts .identicon{
  display: inline-block;
}

.page-accounts td div {
  padding: 0 !important;
}
</style>