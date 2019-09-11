<script>
    export default {
        name: 'TransitionExpand',
        functional: true,
        render(createElement, context) {
            const data = {
                props: {
                    name: 'expand',
                },
                on: {
                    afterEnter(element) {
                        element.style.height = 'auto';
                    },
                    enter(element) {
                        element.style.width = getComputedStyle(element).width;
                        element.style.position = 'absolute';
                        element.style.visibility = 'hidden';
                        element.style.height = 'auto';

                        const height = getComputedStyle(element).height;

                        element.style.width = null;
                        element.style.position = null;
                        element.style.visibility = null;
                        element.style.height = '0';

                        getComputedStyle(element).height;

                        setTimeout(() => {
                            element.style.height = height;
                        });
                    },
                    leave(element) {
                        element.style.height = getComputedStyle(element).height;

                        getComputedStyle(element).height;

                        setTimeout(() => {
                            element.style.height = '0';
                        });
                    },
                }
            };
            return createElement('transition', data, context.children);
        },
    };
</script>
