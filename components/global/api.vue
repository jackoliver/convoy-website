<template>
	<div>
		<section v-for="(path, index) in paths">
			<div>
				<h2>{{ path.name }}</h2>
				<p>
					{{ path.description }}. Lorem ipsum dolor sit amet consectetur adipisicing elit. Debitis molestiae provident eum quas voluptates, incidunt quaerat ex aspernatur nam enim atque nemo facilis
					temporibus nulla commodi aut iusto, totam aliquam!
				</p>
			</div>

			<template v-for="(item, index) in path.paths">
				<template v-for="(itemMethod, index) in item.methods">
					<h3 class="path--method--head">
						<span class="tag" :class="itemMethod[0]">{{ itemMethod[0] }}</span>
						{{ item.path }}
					</h3>
					<p>{{ itemMethod[1].description }}</p>

					<template v-if="itemMethod[1].parameters">
						<h5>Parameters</h5>
						<ul>
							<li v-for="(parameter, index) in itemMethod[1].parameters">
								<p>
									<span class="value">{{ parameter.name }}</span>
									<span class="tag grey">{{ parameter.schema.type }}</span>
									:
									{{ parameter.description }}
								</p>
							</li>
						</ul>
					</template>
				</template>
			</template>
		</section>
	</div>
</template>

<script>
import SwaggerParser from '@apidevtools/swagger-parser';

export default {
	layout: 'docs',
	data() {
		return {
			api: {},
			paths: []
		};
	},
	async mounted() {
		let parser = new SwaggerParser();
		const spec = await parser.validate('openapi3.json');
		console.log('🚀 ~ file: api.vue ~ line 93 ~ mounted ~ spec', spec);

		const paths = Object.entries(spec.paths);
		const items = [];
		const tags = spec.tags;
		tags.forEach(tag => {
			const item = paths.filter(path => path[1].get?.tags[0] === tag.name);
			const path = [];
			item.forEach(newItem => path.push({ path: newItem[0], methods: Object.entries(newItem[1]) }));
			items.push({ ...tag, paths: path });
		});

		console.log('🚀 ~ file: api.vue ~ line 104 ~ mounted ~ items', items);
		this.paths = items;

		// spec.then(data => {
		// 	console.log(data);
		// 	this.api = data;
		// 	this.paths = Object.entries(data.paths);
		// 	console.log('🚀 ~ file: api.vue ~ line 40 ~ SwaggerParser.validate ~ this.paths', this.paths[0]);
		// });

		let api = await parser.parse('openapi3.json');
		console.log('🚀 ~ file: api.vue ~ line 101 ~ mounted ~ api', api);
	},
	methods: {}
};
</script>

<style lang="scss" scoped>
.tag {
	padding: 3px 7px;
	border-radius: 10px;

	&.get {
		background: #edf2f7;
		color: #477db3;
	}

	&.post {
		background: #e9f9f1;
		color: #25c26e;
	}

	&.delete {
		background: #ffeeed;
		color: #ff554a;
	}

	&.put {
		background: #e9f9f1;
		color: #f0ad4e;
	}

	&.grey {
		background: #eaebed;
		color: #474852;
	}
}

.path--method--head {
	font-size: 16px;

	span {
		font-weight: 300;
	}

	& + p {
		font-size: 16px;
		border-bottom: 1px solid #eee;
		padding-bottom: 15px;
		padding-top: 10px;
	}
}

ul {
	li {
		border-bottom: 1px solid #eee;

		.tag {
			font-size: 14px;
		}

		.value {
			font-style: italic;
		}

		&:first-of-type {
			margin-top: 0;
		}
	}
}
</style>
