<template>
	<div class="upload">
		<zms-small-header />

		<zms-slide header="Plugins">
			<div class="icons">
				<div>
					<zms-small-button icon="file-archive" text="Download as ZIP" @click="downloadAsZip()" />
				</div>

				<div :class="['verified', ['unverified', 'not-yet-verified', ''][plugin.verified + 1]]">
					<icon v-if="admin" name="thumbs-up" @click.native="verify(1)" />

					<template v-if="plugin.verified == 1">
						VERIFIED
					</template>
					<template v-else-if="plugin.verified == -1">
						DANGEROUS
					</template>
					<template v-else>
						NOT YET VERIFIED
					</template>

					<icon v-if="admin" name="thumbs-down" @click.native="verify(-1)" />
				</div>
			</div>

			<h2>{{plugin.title}} <i>by {{plugin.cert_user_id}}</i></h2>
			<div class="desc">To install a plugin, open <b>Store</b> tab in your blog's admin panel</div>

			<div class="desc">{{plugin.description}}</div>
		</zms-slide>
	</div>
</template>

<style lang="sass" scoped>
	img
		width: 100%
		box-shadow: 0 4px 4px #CCC
		border-radius: 4px

	h2 i
		font-weight: normal
		color: #444

	.desc
		margin-bottom: 16px


	.icons
		float: right
		margin-bottom: 32px
	.icons > *
		display: inline-block
		margin-left: 16px
		vertical-align: top

	.verified
		display: inline-block
		padding: 12px
		height: 21px

		background-color: #080
		color: #FFF

		border-radius: 4px
		box-shadow: 0 4px 4px #CCC

	.not-yet-verified
		background-color: #880
	.unverified
		background-color: #800
</style>

<script type="text/javascript">
	import Plugins from "../../libs/plugins.js";
	import "vue-awesome/icons/file-archive";
	import "vue-awesome/icons/thumbs-up";
	import "vue-awesome/icons/thumbs-down";

	export default {
		name: "view-plugin",
		data() {
			return {
			};
		},

		asyncComputed: {
			plugin: {
				async get() {
					const {address, title} = this.$router.currentParams;
					return await Plugins.get(address, title);
				},
				default: {
					title: "",
					description: "",
					zip: "",
					cert_user_id: "",
					verified: 1
				}
			}
		},

		computed: {
			admin() {
				return this.$root.siteInfo.settings.own;
			}
		},

		methods: {
			downloadAsZip() {
				top.location.href = this.plugin.zip;
			},

			async verify(value) {
				const {address, title} = this.$router.currentParams;
				await Plugins.verify(address, title, value);
				this.plugin.verified = value;
			}
		}
	};
</script>